---
author: dinolupo
comments: true
date: 2007-06-15 10:35:13+00:00
layout: post
slug: enable-log4j-on-eclipse-environment
title: enable LOG4J on Eclipse environment
wordpress_id: 16
categories:
- Eclipse
---

If you try to run a program inside eclipse, have you ever seen this:  

log4j:WARN No appenders could be found for logger (com.test.TestClass).  
log4j:WARN Please initialize the log4j system properly.
<!--more-->

To initialize the log4j system you have to:

1) create a log4j.properties file like this:  
  {% highlight properties linenos %}  
  log4j.rootCategory=TRACE, CONSOLE  
  log4j.appender.CONSOLE=org.apache.log4j.ConsoleAppender  
  log4j.appender.CONSOLE.Threshold=INFO  
  log4j.appender.CONSOLE.layout=org.apache.log4j.PatternLayout  
  log4j.appender.CONSOLE.layout.ConversionPattern=- %m%n  
  {% endhighlight %}

2) zip the file and rename it .jar (log4jprops.zip => log4jprops.jar), put the file in a folder you like (example: /workspace/log4jprops/log4jprops.jar)

3) in Eclipse: Run, Debug, click the configuration you want, select Classpath tab  

4) click on the User Entries item in the tree view to select it  

5) click advanced, select "Add Classpath Variables" radio, and click OK  

6) select or create a variable named LOG4J_PROPS, and point it to the JAR created before.  
  
That's all folks!  

