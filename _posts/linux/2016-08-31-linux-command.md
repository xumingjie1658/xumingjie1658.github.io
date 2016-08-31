---
layout: post
title: "linux指令汇总"
description: ""
category: [linux, chinese]
tags: [linux, command]
---
{% include setup %}

source:

```
source filename [arguments]
Read  and  execute  commands from filename in the current shell environment and return the exit status of the last command executed from filename.
If filename does not contain a slash, file names in PATH are used to find the directory containing filename.  The file searched for in  PATH  need
not  be  executable.   When bash is not in posix mode, the current directory is searched if no file is found in PATH.  If the sourcepath option to
the shopt builtin command is turned off, the PATH is not searched.  If any arguments are supplied, they  become  the  positional  parameters  when
filename  is  executed.  Otherwise the positional parameters are unchanged.  The return status is the status of the last command exited within the
script (0 if no commands are executed), and false if filename is not found or cannot be read.
```

example: source ~/.bash_profile