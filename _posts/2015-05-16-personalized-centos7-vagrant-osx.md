---
layout: post
title: "How To Build a Custom CentOS 7 Box in Vagrant (OSX Yosemite) From Scratch"
comments: true
categories: osx vagrant centos
---

Are you familiar with Vagrant? Beside the official definition [Why Vagrant](http://docs.vagrantup.com/v2/why-vagrant/index.html), in case you need a fast and solid environment to try out your software on VMs, then you have to give Vagrant a try because it can be seen as a wrapper on top of virtualization software that can speed up your work a lot. At the end of this tutorial you will be able to start new personalized machines in a matter of seconds.<!--more-->

In this How To we are going to prepare a Vagrant local environment box VM based on CentOS 7 minimal image, with dev support and some basic improvements.

### Preliminary Steps: 

- [Download Vagrant](http://www.vagrantup.com/downloads)
- [Oracle VirtualBox](https://www.virtualbox.org/wiki/Downloads)

### Create a New Virtual Machine on Virtual Box with the following settings:

- Remove audio and USB
- New Virtual Disk: [Type: VMDK, Size: 40 GB]
- Verify that your main network is NAT Add this port-forwarding rule: [Name: SSH, Protocol: TCP, Host IP: blank, Host Port: 2222, Guest IP: blank, Guest Port: 22]
- Mount your CentOS 7 iso image and startup the VM

### During installation, pay attention to the following steps:

- create a user _vagrant_ with password _vagrant_ and check the administrator option (click twice Done to accept this insecure password)
- set root password as _vagrant_ (twice Done again)

### After Reboot do the following steps:

- If you do not have a pair of RSA keys, generate these in OSX Terminal with the following command, leave password blank:

```ssh-keygen -t rsa```

Example:
![OSX Terminal ssh-keygen -t rsa]({{site.baseurl}}/assets/images/2015-05-16-SS1.png)

We will need those two files ```id_rsa``` and ```id_rsa.pub``` in the following steps.

- login into the VM as root doing 
```
ssh root@127.0.0.1 -p 2222
``` using password _vagrant_

If you have a LOCALE error inside the VM console, then you can solve the problem doing the following in OSX Terminal:

### (OPTIONAL) LOCALE PROBLEM ON OSX via SSH
prevent sending LC_ALL variable to the server: 

edit ```/etc/ssh_config``` and comment the following line with a #:

``` #	SendEnv LANG LC_*```

### Setup the CentOS VM with base software:

```yum groupinstall base``` 

### Configure SSH with no password login, this is crucial because without, Vagrant has a problem with ssh'ing VM asking for password:

- ```vi /etc/ssh/sshd_config```

- set no Password Authentication:

```PasswordAuthentication no```

### Setup the ```vagrant``` user to be able to execute ```sudo``` commands without prompting you for a password:

```visudo -f /etc/sudoers.d/vagrant```

add in that file: 

```vagrant ALL=(ALL)       NOPASSWD: ALL```

and add the following line to the sudoers file doing ```visudo```:

```Defaults:vagrant !requiretty```

- test the configuration with the following command:

```sudo pwd```

It will return the current working directory without asking for a password. If you are prompted for a password, something is wrong.

### Install the public key ```id_rsa.pub``` in the VM

This is the way Vagrant will communicate with the VM. In OSX I had problems with "insecure vagrant key" and after googling a lot I realized that creating a fresh pair of keys solved the problem:

{% highlight sh %}
mkdir -p /home/vagrant/.ssh
chmod 0700 /home/vagrant/.ssh
vi /home/vagrant/.ssh/authorized_keys
{% endhighlight %}

copy ```id_rsa.pub``` content in this file (or use scp to transfer the file into the VM)

Example file content:
![ssh authorized_keys example]({{site.baseurl}}/assets/images/2015-05-16-SS2.png)

{% highlight sh %}
chmod 0600 /home/vagrant/.ssh/authorized_keys
chown -R vagrant:vagrant /home/vagrant/.ssh
service sshd restart
{% endhighlight %}

### Install Guest Tools 

- Host-D to mount Guest Tools CD-ROM (if centos iso is mounted, a screen like the following appears and you have to select force unmount)
![VirtualBox Accept]({{site.baseurl}}/assets/images/2015-05-16-SS3.png)

yum install gcc make 
yum install "kernel-devel-uname-r == $(uname -r)"
mount /dev/sr0 /mnt -r
cd /mnt
./VBoxLinuxAdditions.run

### Enhance your Centos bash experience with these files:

- [.vimrc](https://gist.github.com/b0cbcec4101b6290e6d6.git) change colorscheme and add <SPACE> command to clear search in VIM
- [.bashrc](https://gist.github.com/276de2a657ff8258f367.git) add a great command prompt for bash
- [.dircolors](https://gist.github.com/105c1e9287409d3d5edb.git) I hate that blue color on shell folders...

source .bashrc

**Note**

copy the same files in ```/root``` folder and 

vi /etc/hostname
centos7-enhanced.localdomain


### Correct vagrant 1.7.2 installation because of [this bug](https://github.com/mitchellh/vagrant/issues/5070)

- From OSX Terminal (HOST machine) do the following:

```sudo vi /opt/vagrant/embedded/gems/gems/vagrant-1.7.2/plugins/communicators/ssh/communicator.rb```

line 171 : add .env after @machine :

```@machine.env.data_dir.join("private_key").open("w+") do |f|```

![vagrant file correction]({{site.baseurl}}/assets/images/2015-05-16-SS4.png)

- Without shutting down the VM, open a new OSX Terminal, create a folder and write the following:

```vagrant package --base vagrant-centos7-enhanced```

You can rename the file with:

```mv package.box centos7-enhanced.box```

![Correct execution]({{site.baseurl}}/assets/images/2015-05-16-SS5.png)

### Test your new base BOX

```vagrant box add centos7 centos7-enhanced.box```

```vagrant init centos7```

- modify Vagrantfile with the follwing, so you tell vagrant to use your private key to SSH without prompting for password:

{% highlight sh %}
  # SSH Agent Forwarding
  #
  # Enable agent forwarding on vagrant ssh commands. This allows you to use ssh keys
  # on your host machine inside the guest. See the manual for `ssh-add`.
  config.ssh.private_key_path = '~/.ssh/id_rsa'
  config.ssh.forward_agent = true
{% endhighlight %}

- to start the brand new box execute the following:

```vagrant up```

You can open other shells in the VM by executing:

```vagrant ssh```

If you want to ssh with standard commands, follow this link [SSH login without password](http://www.linuxproblem.org/art_9.html).


## References:
[SSH login without password](http://www.linuxproblem.org/art_9.html)

[StackOverflow Kernel Devel Differences](http://unix.stackexchange.com/questions/110682/yum-installs-kernel-devel-different-from-my-kernel-version)

[Vagrant package problem resolution](https://github.com/mitchellh/vagrant/issues/5070)

[SSH Login without password](http://www.linuxproblem.org/art_9.html)

[SSH Config Reference](http://docs.vagrantup.com/v2/boxes/base.html)

[install tools on centos 7 minimal](http://itekblog.com/centos-7-virtualbox-guest-additions-installation-centos-minimal/)
