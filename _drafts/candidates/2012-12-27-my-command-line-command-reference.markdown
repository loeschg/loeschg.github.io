---
layout: post
status: publish
published: true
title: My Command-line Command Reference
author:
  display_name: Greg Loesch
  login: greg
  email: loesch.greg@gmail.com
  url: http://gregloesch.com
author_login: greg
author_email: loesch.greg@gmail.com
author_url: http://gregloesch.com
wordpress_id: 880
wordpress_url: http://gregloesch.com/?p=880
date: '2012-12-27 10:58:54 -0500'
date_gmt: '2012-12-27 15:58:54 -0500'
categories:
- tech
- programming
tags:
- wordpress
- Git
- command-line
- file permissions
- terminal
- common command-line
comments: []
---
<p>The goal is for this post to be a one stop shop for some of my commonly used/helpful command-line commands. It's for personal reference, though perhaps you could benefit. List will be updated.</p>
<pre># Quickly switch back to previous directory 
$ cd -

# Repeat previous command with sudo 
#(!! is shorthand for the last command)
$ sudo !!

# Copy foobar.txt from remote.com to local/directory 
$ scp username@remote.com:foobar.txt /local/directory

# Change file permissions of all directories in [DIR_ROOT] to 775.
$ find [DIR_ROOT] -type d -exec chmod 775 {} +;

# Change file permissions of all files in [DIR_ROOT] to 664.
$ find [DIR_ROOT] -type f -exec chmod 664 {} +;

# squash the last [x] number of commits 
# (used to maintain a cleaner git history)
$ git rebase -i HEAD~[x]</pre>
