---
layout: post
title: "Ruby, RVM, Cmd Line SheBang!"
date: 2014-02-12 10:56:35 -0500
comments: true
categories: Tips Ruby
---
I ran across this tip while reading the book <em>"Build Awesome Command-Line Applications
in Ruby 2,"</em> by David Bryant Copeland.  Although most of the code I've been practicing
has been Ruby on Rails, at my day job we utilize a lot of command line applications.  I'm on a mission to add Ruby to our toolbox which currently is Perl and shell scripts.

For a recent project I needed a command line application that could take a CSV
file as an input, extract out some data, and create a new pipe (|) delimted text
file.  I use RVM on my personal laptop and desktop, so I installed it on our 
company server.  At that time, Ruby was at version 1.93.  Not too long after, I
added 2.0.0 and 2.1.0.  But my application was written in 1.93.  It is a simple
application so the version shouldn't matter.  But in case it does, you may find
value in this tip.

If you are a RVM user, and type the command 'which ruby' at the command line, you
may get something similar to:

```
/Users/jfhogarty/.rvm/rubies/ruby-2.1.0/bin/ruby
```

You can use that as the first line of your ruby script:
```
#!/Users/jfhogarty/.rvm/rubies/ruby-2.1.0/bin/ruby
```

However, if you want your script to use whatever the current version of Ruby is,
or if Ruby is in a different location on a machine other than yours, you can use this syntax:

```
#!/usr/bin/env ruby
```

This tip is found on page 4, in the section: "Shebang: How the Systems Knows an App Is A Ruby Script"
