---
layout: code
title:  "Awww, yeah, the python repl setup I've needed."
date:   2017-08-06 20:20:00 -0500
tags: python 
---

One of my favorite things about working with clojure is how easy it is to integrate vim with the repl. I've been wanting something like that with python, but I've I couldn't find what I needed to make it work, until now. 

The autoreload module does what you'd expect, it automatically reloads files that you've imported after they've been changed. This lets you import a file into an ipython repl, change that file in your editor (vim, duh!) and then have those changes automatically available in the repl. 

To try this out, do this. Launch and ipython repl at the command line in a directory where you are going to work on a file. Then, run this:

{% highlight python %}
In [1]: %load_ext autoreload
In [2]: %autoreload 2
{% endhighlight %}

The first line loads the autoload extension into ipython and the second sets it so that it will autoload all the modules except those specifically excluded. You can read more about it all [here](https://ipython.org/ipython-doc/3/config/extensions/autoreload.html)
