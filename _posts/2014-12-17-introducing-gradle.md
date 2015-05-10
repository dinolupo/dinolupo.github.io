---
layout: post
title: "Introducing Gradle"
comments: true
categories: devops gradle
---

Gradle is a build system with many improvements over Ant and Maven. In this post I will go through an Hello World example on how I set up gradle on OSX and work along with eclipse. With few changes you can adapt this mini-guide on Windows and Linux.<!--more-->

I installed gradle [binary only distribution](https://services.gradle.org/distributions/gradle-2.4-bin.zip) version 2.3  on [Oracle JDK 7](http://docs.oracle.com/javase/7/docs/webnotes/install/). 

The only prerequisite to install gradle is to have a working Java 6 or higher JDK or JRE, to verify use *java -version* on a terminal.


* Download the latest binaries from [here](https://gradle.org/downloads/)
* unpack into a folder with the unzip command 

{% highlight sh %}
unzip gradle-2.4-bin.zip
{% endhighlight %}

for example I unzipped into the following (see path after the "pwd" command):

{% highlight sh %}
[17:56:48] [dino@settedinove /Users/dino/bin/gradle]$ pwd
/Users/dino/bin/gradle
{% endhighlight %}

* update your path to add bin folder inside your environment adding this line to your ".bash_profile" in your home folder (I use "vi" editor in the example below):

{% highlight sh %}
[18:03:10] [dino@settedinove /Users/dino]$ vi .bash_profile
{% endhighlight %}

Append the following export command:

{% highlight sh %}
export PATH=$PATH:~/bin/gradle/bin
{% endhighlight %}

Save and close the file. To apply changes immedialty enter:
{% highlight sh %}
source /.bash_profile
{% endhighlight %}

* Verify your installation with "gradle tasks" command:

{% highlight sh %}
[18:07:41] [dino@settedinove /Users/dino]$ gradle tasks
:tasks

------------------------------------------------------------
All tasks runnable from root project
------------------------------------------------------------

Build Setup tasks
-----------------
init - Initializes a new Gradle build. [incubating]
wrapper - Generates Gradle wrapper files. [incubating]

Help tasks
----------
components - Displays the components produced by root project 'dino'. [incubating]
dependencies - Displays all dependencies declared in root project 'dino'.
dependencyInsight - Displays the insight into a specific dependency in root project 'dino'.
help - Displays a help message.
projects - Displays the sub-projects of root project 'dino'.
properties - Displays the properties of root project 'dino'.
tasks - Displays the tasks runnable from root project 'dino'.

To see all tasks and more detail, run gradle tasks --all

To see more detail about a task, run gradle help --task <task>

BUILD SUCCESSFUL

Total time: 6.578 secs
{% endhighlight %}

Now that you have a working gradle setup, you can download my [github project](https://github.com/dinolupo/hello-gradle.git) and try to execute some gradle commands on it like the following:

{% highlight sh linenos %}
# build to download libraries into your local repository
gradle build
# update eclipse project to reflect new changes about the Java build path
gradle eclipse
{% endhighlight %}

# build.gradle explanation

{% highlight groovy linenos %}
apply plugin: 'java'
apply plugin: 'eclipse'
apply plugin: 'application'

mainClassName = 'hello.HelloWorld'

// tell gradle the file name for jar task
jar {
    baseName = 'hello-gradle'
    version =  '1.0.0'
}

// The repositories block indicates that the build should resolve its dependencies from the Maven Central repository
repositories {
    mavenCentral()
}

// project dependencies, when you update dependencies always do a build and eclipse task to regenerate eclipse project with new dependencies
dependencies {
    compile "joda-time:joda-time:2.7"
}

// gradle standalone task (useful if you do not have gradle installed and download the project from github, generate executable for Windows/Unix to download automagically gradle)
task wrapper(type: Wrapper) {
    gradleVersion = '2.3'
}
{% endhighlight %}

This is my Blog Access Memory post, to go deeper have a look at the full tutorial reference below.

Dino.

### References

[http://www.cyberciti.biz/faq/appleosx-bash-unix-change-set-path-environment-variable/](http://www.cyberciti.biz/faq/appleosx-bash-unix-change-set-path-environment-variable/)

[https://spring.io/guides/gs/gradle/](https://spring.io/guides/gs/gradle/)

