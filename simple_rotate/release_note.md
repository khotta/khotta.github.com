---
layout: sr/default
title: Release notes
place: first
---
> ### 1.2.0 - the 19th of January, 2017
>> #### About the change
- Changes the prefix of tempfile name from '.SimpleRotate_tempfile_' to '.simple_rotate_tempfile_'.
- Trys to replace whitespace character(s) '/\s/' on tempfile name to '_'.
- Allows remaining a temfile even if at_exit wasn't called.
    A tempfile must be remain when your program is finished with some signals such as dosen't call #at_exit.  
    That case will happen when process was received some signals like INT, TERM, ...   
    I tried to handle this by trapping signals, but when it's received KILL signal SimpleRotate can't handle this.   
    That why I abandoned to remove a tempfile precisely bacause I think it's impossible.  
    I mention that in the case of that #at_exit wasn't called a tempfile must be remain.   
    But if a tempfile is remaining, file lock must be done as well and SimpleRotate will provide you Mutex.   
    So I hope you don't mind that.   

> ### 1.1.1 - the 13th of December, 2016
>> #### About the change
- Fixed the some warning errors.
- Fixed the problem nil convert to empty string.

> ### 1.1.0 - the 30th of March, 2016

>> #### About the change
- Refactoring; Separated files from a file for easy to understand the source code.
- Changed a method name from **SimpleRotate#psafe_mode** to **SimpleRotate#psync**.
- Changed default value of limit to "100M" in **SimpleRotate#init**.

> ### 1.0.0 - the 3rd of February, 2013
>> #### First release
