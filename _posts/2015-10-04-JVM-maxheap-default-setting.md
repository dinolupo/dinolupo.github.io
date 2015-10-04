---
layout: post
title: "JVM default maxheap setting"
comments: true
categories: Java
---

I was working with some samples involving Eclipse, Spring Cloud and Microservices architecture, and trying to execute the complete environment setup on my laptop with 8 gigs of ram. It became very hot and noisy with the fan at maximum. The system was very slow and unresponsive and I thought WTF a quad core with 8 gigs is so slow running a bunch of java applications? After going deeper into the problem, I discovered that the default JVM settings for the max heap is a 1/4 of the system RAM! <!--more-->

Execute the following command to verify how much is the default setting for the parameter ```MaxHeapSize```

{% highlight sh %}
java -XX:+PrintFlagsFinal -version | grep MaxHeapSize
{% endhighlight %}

On my machine the value is the following:

{% highlight sh %}
uintx MaxHeapSize                              := 2147483648                          {product}
{% endhighlight %}

That is 2GB of Ram for every process!

Then I decided to configure the default behaviour of eclipse to execute processes with lesser value of "MaxHeapSize":

1. Go to Eclipse -> Preferences -> Java -> Installed JREs

2. Select your JRE and click Edit

3. Add this command to the Default VM Argument field:
```-Xmx32m```

It stands for maximum 32MB heap size.

I suggest to keep this value low and override it only if needed in Run/Debug configurations. This way you are doing a very elementary  memory requirement analysis for your services without spending too much effort. Better than nothing ;)   
