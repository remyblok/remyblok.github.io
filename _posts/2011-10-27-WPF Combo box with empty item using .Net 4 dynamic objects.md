---
title: WPF Combo box with empty item using .Net 4 dynamic objects
tags: [".Net",  "C#", "WPF", "Combobox", "Dynamic"]
---
From the moment I’ve started to use WPF I have struggled to get a selectable empty item in a combo box. This is not something that WPF supports out of the box. I had sincerely hoped they would fix this in WPF 4.0, but unfortunately they did not. They did give us dynamic object, which makes the task a lot easier that it was in WPF 3.5.
<!--more-->
There are some solutions to do this in .Net 3.5 like WPF `ComboBox` with empty item on Stack Overflow, but those solutions have some serious limitations. The solution I present is based on another article that uses dynamics to get an extra empty item. That solution has a serious problem if the Type displayed in the combo box does not have a default constructor, the `Activator` will be unable to create an instance. It also requires a generic collection as input, where some specific collections are not generic and thus cannot be used.

Just as the previous examples, my solution uses a `Converter` to add an extra item to the returned collection. But instead of adding an item of the same type I add a dynamic object. Dynamic objects can pretend to contain any property or method. So my dynamic object pretend to contain all the properties of the other items in the collection, but all its values are null. How does this look in code:

```csharp
public class ComboBoxEmptyItemConverter : IValueConverter
{
    /// <summary>
    /// this object is the empty item in the combo box. A dynamic object that
    /// returns null for all property request.
    /// </summary>
    private class EmptyItem : DynamicObject
    {
        public override bool TryGetMember(GetMemberBinder binder, out object result)
        {
            // just set the result to null and return true
            result = null;
            return true;
        }
    }

    public object Convert(object value, Type targetType, object parameter, CultureInfo culture)
    {
        // assume that the value at least inherits from IEnumerable
        // otherwise we cannot use it.
        IEnumerable container = value as IEnumerable;
 
        if (container != null)
        {
            // everything inherits from object, so we can safely create a generic IEnumerable
            IEnumerable<object> genericContainer = container.OfType<object>();
            // create an array with a single EmptyItem object that serves to show en empty line
            IEnumerable<object> emptyItem = new object[] { new EmptyItem() };
            // use Linq to concatenate the two enumerable
            return emptyItem.Concat(genericContainer);
        }
 
        return value;
    }
 
    public object ConvertBack(object value, Type targetType, object parameter, CultureInfo culture)
    {
        throw new NotImplementedException();
    }
}
```

Does this solution have any caveats? Unfortunately it does.

First of all, your combo box need a `DisplayMemberPath` specified. If this property is not set, the Empty Item will be shown as the default `ToString()` value. This might be fixed by overriding the `ToString` in the `EmptyItem` class. Because of this you cannot use it for primitive types (e.g. `int`, `double`, `bool`). As an alternative to the `DisplayMemberPath` you can create an `itemtemplate` for the combo box.

The `ComboxBox` also needs a `SelectedValuePath`. The path must be set to a property that can contain null, because the `EmptyItem` return `null` for everything. If the `SelectedValuePath` point to a primitive or a struct, the value is not set and the empty item not selected (it shows up as selected, but the underlying data won’t change). In theory both situations could be prevented with an extra `ValueConverter` on the `SelectedValue`, that converts from the `EmptyItem` to a default you supply as converter parameter.

Eventually it looks like this in XAML:
```XML
<ComboBox ItemsSource="{Binding  TestObjectCollection, Converter={StaticResource ComboBoxEmptyItemConverter}}"
          SelectedValue="{Binding SelectedID}"
          SelectedValuePath="ID"
          DisplayMemberPath="Name" />
```