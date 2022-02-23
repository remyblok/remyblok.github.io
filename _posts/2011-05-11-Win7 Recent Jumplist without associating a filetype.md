---
title: Win7 Recent Jumplist without associating a filetype
tags: [".Net",  "C#", "jumplist", "Windows 7", "Windows", "Win32", "interop" ]
---
I was trying to add [Windows 7 Jumplists](http://windows.microsoft.com/nl-NL/windows7/products/features/jump-lists) to an .Net 3.5 application. I wanted to add a list of recent opened files. When using .Net 4.0 there is native Framework support in [System.Windows.Shell](http://msdn.microsoft.com/en-us/library/system.windows.shell.aspx)

When interacting with Windows 7 features I always use the [Windows Api Code Pack](http://archive.msdn.microsoft.com/WindowsAPICodePack). It's a library containing all kinds of functionality that is not a part of the .Net framework, but can be reached through the Win32 api. Jumplist support is one of these features.

When using the functionality build into the Code Pack it is mandatory that the type of file that is added to the JumpList is associated with the program. My application does not have files, at least not on disk. So there is no association with any file type. Under the hood the Code Pack uses the Win32 function [SHAddToRecentDocs](http://msdn.microsoft.com/en-us/library/bb762105(VS.85).aspx). Reading the MSDN page makes it clear that there are other ways to add items to the Recent list: An `IShellLink` object that identifies the item through a shortcut.

I created a call to `SHAddToRecentDocs` with as parameter an `IShellLink`. Although `IShellLink` is defined by the Code Pack, the Interface is not publicly accessible therefore we need to define it again. Unfortunately we also need to redefine the `ShellAddToRecentDocs` because it, too, is internally defined the Code Pack.

```C#
[ComImport,
    Guid("000214F9-0000-0000-C000-000000000046"),
    InterfaceType(ComInterfaceType.InterfaceIsIUnknown)]
internal interface IShellLinkW { }
 
[DllImport("shell32.dll")]
internal static extern void SHAddToRecentDocs(
    ShellAddToRecentDocs flags,
    [MarshalAs(UnmanagedType.Interface)] IShellLinkW link);

internal enum ShellAddToRecentDocs
{
    Pidl = 0x1,
    PathA = 0x2,
    PathW = 0x3,
    AppIdInfo = 0x4,       // indicates the data type is a pointer to a SHARDAPPIDINFO structure
    AppIdInfoIdList = 0x5, // indicates the data type is a pointer to a SHARDAPPIDINFOIDLIST structure
    Link = 0x6,            // indicates the data type is a pointer to an IShellLink instance
    AppIdInfoLink = 0x7,   // indicates the data type is a pointer to a SHARDAPPIDINFOLINK structure 
}
```

We don’t have to actually define body of the interface, because we only use it as a definition for the underlying COM interface. The Code Pack does provide a wrapper around `IShellLinks` called `JumpListLink`. With this class we can create a simple link to any application or document we want.

```C#
// Create a JumpListLink with the path of the application and the name of the file
// the name is displayed in the recent items list
JumpListLink link = new JumpListLink(@"c:\windows\notepad.exe", "notepad");
link.Arguments = @"c:\Users\RJB\Desktop\file.txt"; //any argument for the starting application
link.IconReference = new IconReference(@"c:\windows\notepad.exe", 0); //set an icon for the link
```

The problem is that a `JumpListItem` is not an `IShellLinkW`. There is an internal property on `JumpListItem` to get an `IShellLinkW` object. I used reflection to get the value of this internal property. After reading the value of the property it can be used to call the `SHAddToRecentDocs` we just defined.

```C#
private static MethodInfo nativeShellLinkGetMethod;
 
public static void AddToRecent(JumpListLink link)
{
    if (nativeShellLinkGetMethod == null)
    {
        //find the NativeShellLink property on the JumpListLink
        Type jumpListLinkType = typeof(JumpListLink);
        PropertyInfo nativeShellLinkProperty = jumpListLinkType.GetProperty("NativeShellLink",
                System.Reflection.BindingFlags.Instance | System.Reflection.BindingFlags.NonPublic);
 
        if (nativeShellLinkProperty == null)
            throw new InvalidOperationException();
 
        //Save the Method info for later use, so we have to do the reflection only once.
        nativeShellLinkGetMethod = nativeShellLinkProperty.GetGetMethod(true);
    }
 
    //Get the value of the NativeShellLink property.
    //Cast this to our own implementation of IShellLinkW because it is using COM interop.
    IShellLinkW nativeShellLink = (IShellLinkW)nativeShellLinkGetMethod.Invoke(link, null);
 
    // Now make the call to Win32 to add the link to the recent items
    SHAddToRecentDocs(ShellAddToRecentDocs.Link, nativeShellLink);
}
```

All that rests is to call the `AddToRecent` method and send the link along.

```C#
AddToRecent(link);
```

Now the application will have a Notepad shortcut to the file in its recent items list. Clicking the entry in the list will start Notepad with the file defined in the argument property. There is no need to register any file type to the application, but the Recent file JumpList can be used.