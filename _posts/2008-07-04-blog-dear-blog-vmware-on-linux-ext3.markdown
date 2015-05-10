---
author: dinolupo
comments: true
date: 2008-07-04 07:42:17+00:00
layout: post
slug: blog-dear-blog-vmware-on-linux-ext3
title: VMware on linux ext3
wordpress_id: 13
categories:
- Linux
---

It's been a long time since I updated this blog....   
  
Here is a tip to work with vmware on linux ext3 filesystem.  
You have to add this line in your .vmx file:  
  
**mainMem.useNamedFile=FALSE**    
  
put that at the end or anywhere you like.  
  
I tried to use Ubuntu 64 and VMware 64 bit to run a windows 2003 server (32 bit) and I did of course the 32-bit library installation in order to work with 32 bit operating systems.  
  
Bye,  
Dino.  

