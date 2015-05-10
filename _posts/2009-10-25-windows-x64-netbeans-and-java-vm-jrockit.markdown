---
author: dinolupo
comments: true
date: 2009-10-25 17:16:46+00:00
layout: post
slug: windows-x64-netbeans-and-java-vm-jrockit
title: Windows x64 - Netbeans and Java vm JRockIt
wordpress_id: 7
categories:
- Java - J2EE
---

Hi all,  
here is the problem:   
running Netbeans in a x64 environment using the JRockIt virtual machine caused a problem like "vm not found".  
<!--more-->  
solution:   
download the zipped version of netbeans and modify the netbeansetcnetbeans.conf to use use  the jrockit vm like the following (use your installation path if different):  
  
netbeans_jdkhome="C:Program FilesJavajrmc-3.1.0-1.6.0"  
  
* cd into C:Program FilesJavajrmc-3.1.0-1.6.0jrebin  

* make a copy of the "jrockit" folder (and all it's contents) and call the new folder "client"  
  
* run netbeans.exe.  
  
The problem is the following line in the source code for netbeans.exe:  

{% highlight java linenos %}
   static char *jvm_names[] = { "hotspot", "client", "server",
                                "classic" };  
{% endhighlight %}

if I rewrite that line to include "jrockit" as well it works perfectly.  
  
Bye,  
Dino.  
  

