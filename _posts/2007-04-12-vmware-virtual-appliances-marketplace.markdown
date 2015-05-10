---
author: dinolupo
comments: true
date: 2007-04-12 15:40:27+00:00
layout: post
slug: vmware-virtual-appliances-marketplace
title: 'VMWare: Virtual Appliances Marketplace'
wordpress_id: 18
categories:
- Linux
---

A Virtual Appliance is a pre-installed and configured virtual machine. In short VMware has created a market of virtual machines. Many of those are free.  
<!--more-->
Here is the link: [http://www.vmware.com/vmtn/appliances/directory/](http://www.vmware.com/vmtn/appliances/directory/)   
  
Here are some links to virtual appliances that I found very useful:  
  
- [Bugzilla](http://www.vmware.com/vmtn/appliances/directory/807). Bug tracking system; only thing you have to do is to configure the mail server.  
- [JanusVM](http://www.vmware.com/vmtn/appliances/directory/392). Protects your internet traffic and identity from hackers, corporations, even 
most governments.  
  
This site has many useful VA: [http://virtualappliances.net/.](http://virtualappliances.net/)  
  
TIP: for the [http://virtualappliances.net/](http://virtualappliances.net/) machines, if you don't have DHCP, follow these steps:  
   1. configure the ethernet in NAT mode;  
   2. connect to the virtual machine with the browser and go to Configuration tab;  
   3. select Configure Networking and select "use static IP", select "Next->";  
   4. insert the IP settings of your network (contact eventually tha administrator for a new IP) and "power off" (up-right screen) the VM.  
   5. Change the VMware ethernet to Bridged mode and start the VM.  
  
That's all...  
Bye!  
  

