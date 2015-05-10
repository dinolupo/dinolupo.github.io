---
author: dinolupo
comments: true
date: 2009-04-02 07:25:58+00:00
layout: post
slug: functionarrow-keys-etc-not-working-in-vmware-on-linux
title: Function/arrow keys, etc. not working in vmware on linux
wordpress_id: 11
categories:
- Linux
---

Some keys like arrows, or ctrl-alt-del do not work under linux with vmware server 2.0.  
  
Here is the solution:  
  
 Add this line to `~/.vmware/config` (create file if necessary):  
 
 {% highlight php linenos %}   
    xkeymap.nokeycodeMap = true
{% endhighlight %}

and reboot.  
  
Also note that the CTRL-ALT-INS on linux vmware may not work, so try instead to use the numeric keypad DEL instead of the INS button.  
  
Bye,  
Dino.  
  

