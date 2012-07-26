---
layout: post
title: "Detect if bat file is running via double click or from cmd window"
date: 2012-07-26 12:35
comments: true
categories: Batch
---

## Problem

Some times we need to see the batch file result. It is easy to see the output 
if we run the batch file from the cmd window. But to open a cmd windows and `cd`
to the directory then type the batch file name is very boring. And we'd like to 
run the batch file by just one click. So we just add a `pause` at the end of the
batch file (or just before each exit).

    :: normal batch stuffs
    bala
    bala

    :: add the pause to see the result when double click form the explorer.
    pause


Now the the double click way works fine. But life is not that easy! Now if we run
it from cmd windows, we needs to press a key to continue. So if we can detect if 
bat file is running via double click or from cmd window, then we can make both
case happy.

But how?

## Solution

I googled for answers, and I found [thisstackoverflow question][stackoverflow-5859854].
The answer says:

`%cmdcmdline%` gives the exact command line used to start the current Cmd.exe.

* When launched from a command console, this var is `"%SystemRoot%\system32\cmd.exe" `.
* When launched from explorer this var is `cmd /c ""{full_path_to_the_bat_file}" "`;

But how to do the check?

I began with this:

	if %cmdcmdline% == "%SystemRoot%\system32\cmd.exe" (
		echo from cmd window...
	) else (
		pause
	)


But that failed because when launched from explorer %cmdcmdline% have `"` and tailing space.

After a lot of test, finally I work out it like this:

	if "%cmdcmdline:~4,2%"=="/c" (pause)

Clear and simple! 

We just check the `/c` in `cmd /c ""{full_path_to_the_bat_file}" "` when from dobule click in exploter.
This is valid because when launched from a command windows, %cmdcmdline% is `"%SystemRoot%\system32\cmd.exe" `.
Where the `%SystemRoot%` is usually `C:\Windows`. And it can hardly have something like `C:\/cndows`.

I tested on Windows XP and Windows 7 both works.


[stackoverflow-5859854]:http://stackoverflow.com/questions/5859854/detect-if-bat-file-is-running-via-double-click-or-from-cmd-window