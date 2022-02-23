---
title: Start all Command Prompts as Visual Studio Developer Command prompt
tags: [".Net",  "C#", "Developer", "cmd", "command prompt", "visual studio"]
---
When developing you often need to use the Command Prompt (cmd.exe) to do something for which there is no easy UI. Visual Studio and Windows SDKs include many CLI applications. It also includes the Visual Studio Command Prompt. This command prompt calls a script that sets all kinds of environment variables so that the CLI apps can be easily started.

Windows provides all kinds of ways to start a Command Prompt: start cmd.exe, Shift+right click a folder, from the File menu in windows 8 etc. But only when the special "Developer Command Prompt" link is used the Command prompt is actually started with the special environment.

That makes it very cumbersome to use the Command prompt, because it is easy to open it from all kinds of places, but when you need to use a CLI app from Visual Studio you need to start another Command Prompt and do your stuff again. Especially when starting the Command Prompt as Administrator is not easy, because the path is never correct. I wanted to fix that.
<!--more-->
Under the hood the "Developer Command Prompt" starts a batch file that does all the magic:

```cmd
"C:\Program Files (x86)\Microsoft Visual Studio 12.0\Common7\Tools\VsDevCmd.bat"
```

Whenever this batch file is called inside a Command Prompt it loads all the environment variables needed. In order to have all Command Prompts do this automatically this batch file needs to be started when cmd.exe is started.

cmd.exe contains a feature named AutoRun. In the registry you can add a key with a reference to a batch file that will be executed for every cmd instance. That's what we want! The Registry key is located here:

```
Path: HKLM\SOFTWARE\Microsoft\Command Processor
Key: AutoRun as Reg_SZ / String
```

I tried putting the VsDevCmd.bat directly in the registry, but this does not work. The batch file starts another instance of cmd, with in turn autoruns the VsDevCmd batch file. This is a never ending loop. So you will need a special batch file that breaks the loop. I have created the following batch file and saved that to my document folder. This batch file is referenced in the registry in the AutoRun key

```cmd
@ECHO OFF
IF "%VsDevCmdRan%" EQU "1" GOTO :EOF
SET VsDevCmdRan=1

"C:\Program Files (x86)\Microsoft Visual Studio 12.0\Common7\Tools\VsDevCmd.bat"
```

Basically this batch file sets an environment variable when it started to run, each new instance of CMD will check this variable and if it is set it will not rerun VsDevCmd.bat. Set this files full path as the AutoRun-key in the registry, do not forget to add quotes (") around the path. Problem solved!

Now whenever a Command Prompt is started under the hood the VsDevCmd.bat is called and I always have a Developer Command Line. It also works as administrator.

Maybe you might want to start a Command Prompt without the VsDevCmd.bat started. This is possible without changing the Registry. You can run the following command:
code:

```cmd
cmd.exe /d
```

The /d-option indicated to suppress the AutoRun configuration.