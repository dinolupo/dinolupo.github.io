---
author: dinolupo
comments: true
date: 2009-04-30 09:43:41+00:00
layout: post
slug: linux-ubuntu-debian-moving-from-one-machine-or-server-to-another
title: 'Linux - Ubuntu - Debian: moving from one machine or server to another.'
wordpress_id: 9
categories:
- Linux
---

Hi,  
  
when you move one Linux virtual machine based on Debian from a phisical server to another, you may see that the network does not work anymore. It is disappeared and when you digit ifconfig you see only lo (localhost).  
<!--more-->  
To solve this go to:  

{% highlight sh linenos %}  
/etc/udev/rules.d  
{% endhighlight %}

and   

{% highlight sh linenos %}  
sudo vim 70-persistent-net.rules  
(or sudo nano 70-persistent-net.rules)  
{% endhighlight %}

and delete all lines.   

{% highlight sh linenos %}  
sudo reboot  
{% endhighlight %}

et voilà! eth0 again!  
  
Explanation: in that file debian associates mac address with ethernet devices, so the old interface is blocked with the old mac address and cannot start since the new vmware server has generated a new mac address for that interface. Deleting it will permit to associate the new mac address to the net interface.  
  
Bye all.  
Dino.  

