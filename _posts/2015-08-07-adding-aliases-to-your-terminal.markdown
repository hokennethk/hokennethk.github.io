---
title:  "Adding Aliases To Your Terminal"
date:   2015-08-07 14:28:00
description: Speed up your terminal workflow
---

There are many different tools available to you as a developer that can help speed up your workflow. This post focuses on adding aliases to my bash profile, which has helped speed up my workflow.

If you plan to develop using OS X, then I suggest you get to know some of your basic terminal commands (I use the basic terminal included with OS X). When you find yourself executing the same commands over and over again, then I suggest you create an alias for that command. An alias is basically a shortcut you can type that will execute its linked command. You can create aliases by adding some lines to your 'bash_profile' file, usually located at '~/.bash_profile'.

**NOTE**: Make sure you edit this file using a plain-text editor. Rich-text formatting could lead to errors!


### Example
Here's an example of what happens when I type 'l' into my terminal:

![formatted_ls]({{ site.url }}/assets/post_images/2015-08-07-formatted-ls.png)

This small command is a shortcut for 'ls -lhFG', which is just the 'ls' command with some flags to format the display. A few of my other aliases include:

{% highlight bash %}
alias prof='sublime ~/.bash_profile'
alias reprof='source ~/.bash_profile'
alias viewblog='blog && bundle exec jekyll serve'
alias coffeecompile='coffee --output compiled --map --  watch --compile ./'
alias serve='bundle exec jekyll serve'
alias orcarebase='git pull --rebase upstream master'
{% endhighlight %}
These are commands I run often, and instead of typing them out, I can type in the alias shortcut word instead.

**NOTE**: The 'alias reprof='source ~/.bash_profile'' alias is a useful command to reload your bash_profile during a terminal session. That way, you don't need to exit and re-open terminal everytime you make a change to your bash_profile.

I also create aliases to 'cd' into directories I use often. Here are some of the shortcuts I've set up:

{% highlight bash %}
alias hr='cd ~/Documents/Hack_Reactor'
alias sprints='cd ~/Documents/Hack_Reactor/sprints'
alias self-assessments='cd ~/Documents/Hack_Reactor/self-assessments'
alias toy='cd ~/Documents/Hack_Reactor/2015-06-toy-problems'
alias github='cd /Users/kennethho/Documents/Github'
alias blog='cd /Users/kennethho/Documents/Github/blog'
alias nodeschool='cd /Users/kennethho/Documents/nodeschool'
{% endhighlight %}

So from anywhere in the terminal, I can navigate quickly to a folder I've set up. Just typing 'nodeschool' will bring me to '/Users/kennethho/Documents/nodeschool'!

Aliases are a great way to help speed up navigating the terminal. Use them often!