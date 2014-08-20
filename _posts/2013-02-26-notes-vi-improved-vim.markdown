---
layout: post
title: 'Notes: Vi IMproved - Vim '
date: '2013-02-26 23:08:08 -0500'
categories:
- dev 
tags:
- vim
- vi
- dev
- vi improved

---
I'm currently reading through the book *Vi IMproved - Vim* and am using this post to share my notes should anyone be interested.

<!--more-->

### Vi IMproved - Vim 


**Undo/redo**
    
    U       - undoes all changes to the line
    ctrl+r  - redoes undo

**Write/quit with ZZ**

**Make new & join lines**

    J       - join the line below with the current line. 
    r<enter> - start a newline from that location.

**Repeat commands**

    .       - Repeat the previous command with the '.' key.

**'G'o to line**
    
    #G      -   Go to a specific line number. No argument assumes end of line.

**Vim macros**
    
    q[character]    -   start recording in register [character]
    <do some stuff> -   
    q               -   q macro
    @[character]    -   execute the macro

**Turn off search highlighting**

    :noh

**Regular expression basics**

    ^   -   beginning of line
    $   -   end of line
    .   -   any character
    \   -   escape character

**"Twiddling"**

    xp  -   Swap two characters that are side by side

**Marks**

    m[char] -   mark location in text
    `[char] -   return to the mark

**Pipe text into file without leaving Vim**

    !![command name]    -   executes [command name] and places output in the file
    e.g. !!ls           -   puts output of ls into file

<!--
status: publish
published: true
author:
  display_name: Greg Loesch
  login: greg
  email: loesch.greg@gmail.com
  url: http://gregloesch.com
author_login: greg
author_email: loesch.greg@gmail.com
author_url: http://gregloesch.com
wordpress_id: 925
wordpress_url: http://gregloesch.com/?p=925
date_gmt: '2013-02-27 04:08:08 -0500'
comments: []
-->