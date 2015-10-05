---
layout: post
title: "JVM default maxheap setting"
comments: true
categories: Java
---

I was working with some Java projects with Eclipse, Gradle, Spring Cloud and Microservices architecture, and trying to execute the complete environment setup on my laptop with 8 gigs of ram. It became very hot and noisy with the fan at maximum. The system was very slow and unresponsive and I thought WTF a quad core with 8 gigs is so slow running a bunch of java applications? After going deeper into the problem, I discovered that the default JVM settings for the max heap is a 1/4 of the system RAM! <!--more-->

### Set a lower default Max Heap settings for your Java processes in Eclipse

Execute the following command to verify how much is the default setting for the parameter ```MaxHeapSize```

{% highlight sh %}
java -XX:+PrintFlagsFinal -version | grep MaxHeapSize
{% endhighlight %}

On my machine the output is the following:

{% highlight sh %}
uintx MaxHeapSize	:= 2147483648	{product}
{% endhighlight %}

That is 2GB of Ram for every process!

Then I decided to configure the default behaviour of eclipse to execute processes with lesser value of "MaxHeapSize":

1. Go to Eclipse -> Preferences -> Java -> Installed JREs

2. Select your JRE and click Edit

3. Add this command to the Default VM Argument field:
```-Xmx32m```

It stands for maximum 32MB heap size.

I suggest to keep this value low and override it only if needed in Run/Debug configurations. This way you are doing a very elementary  memory requirement analysis for your services without spending too much effort. Better than nothing ;)

### Adjust gradle daemon Max Heap default in Eclipse Buildship

Working with the gradle plugin for Eclipse (Buildship) I discovered that it also starts a gradle Daemon with Max Heap settings 1024m for each wrapper version of your opened projects. To reduce the max occupied memory, I suggest the following procedure:

1. If possible use only one version of the gradle wrapper so that eclipse will start only one version of the daemon:
	- delete the folder ```.gradle``` 
	- update  gradleVersion in ```build.gradle```

{% highlight groovy %}
task wrapper(type: Wrapper) {
	gradleVersion = '2.6'
}
{% endhighlight %}   
 
2. create a file in every root project directory named ```gradle.properties``` with the following content:

{% highlight properties %}
org.gradle.jvmargs=-Xms32m -Xmx32m
{% endhighlight %}

This way you are telling the daemon to use at maximum 32MB of Ram for the heap.

You can check the memory occupied by the java processes running ```jconsole``` from the command line. 

Bye,
Dino.
  
