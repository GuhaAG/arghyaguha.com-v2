+++
author = "Arghya Guha"
title = "Kill that pesky process, the really lazy way"
date = "2020-09-05"
description = "Kill that pesky process, the really lazy way"
+++

Write and use a simple bash script to kill a process to free up a port.

This article was first published on my dev.to page, check it here - https://dev.to/guha/kill-that-pesky-process-the-really-lazy-way-5eg.
<!--more-->


This is going to a be a quick one, today i'm going to share a small bash script that has saved me precious seconds or maybe even whole minutes over the years, every time i want to kill a process to free up a port.

## Why

```
Something is already running at port 9000
```

I run into one of these every once in a while. 

With me it often happens when i _SUSPEND_ (<kbd>CTRL</kbd>+<kbd>Z</kbd>) my dev server instead of _KILLING_ (<kbd>CTRL</kbd>+<kbd>C</kbd>) it. 

The next steps would often involve hunting down the `pid` of the process running on that port and killing it.

Easy enough, but here's something easier.

## How

1. `cd` to `/usr/local/bin/`.
2. Create a new file and name it `stop` or anything else you want.
3. Paste the following into it 
    ```
       #!/bin/bash
       touch temp.text
       lsof -n -i4TCP:$1 | awk '{print $2}' > temp.text
       pidToStop=`(sed '2q;d' temp.text)`
       > temp.text
       if [[ -n $pidToStop ]]
       then
       kill -9 $pidToStop
       echo "!! $1 is stopped !!"
       else
       echo "Sorry nothing running on above port"
       fi
       rm temp.text
    ```
    *disclaimer: i didn't write the script, i merely adopted it. Credits below.
4. `chmod +x stop` to make the file executable. `chmod 755` also works.
5. Now you can use `stop 9000` or any other port anytime on the terminal to stop whatever process is running on that port. 
6. Profit. Write once, use always.

_Very briefly, what the script above does is find the `pid` of the process running on the input port, copy it to a temp file and then read the temp file to find the `pid` in it and kill it, finally deleting the temp file. 
`awk` for those curious is a programming language used for text processing._

### Notes
- Here's my [gist](https://gist.github.com/GuhaAG/32ae5f04293e9075e44754785b41f8b9) with the script above.
- Here's the stackoverflow [answer](https://stackoverflow.com/questions/24387451/how-can-i-kill-whatever-process-is-using-port-8080-so-that-i-can-vagrant-up/59986097#59986097) i originally found this script at.
