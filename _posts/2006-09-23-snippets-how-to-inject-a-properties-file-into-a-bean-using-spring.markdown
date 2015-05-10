---
author: dinolupo
comments: true
date: 2006-09-23 09:31:19+00:00
layout: post
slug: snippets-how-to-inject-a-properties-file-into-a-bean-using-spring
title: 'Snippets: how to inject a Properties file into a bean using Spring'
wordpress_id: 24
categories:
- Java - Spring
---

Here is an example on how inject classes with Spring container.
<!--more-->

Let's suppose you have a bean with a Properties property:
    
 {% highlight java linenos %} 
package com.mypackage;

public class MyBeanClass {

Properties myProperties;

//
// other class code
//

    //
    //  don't forget the setter!
    //
    public void setMyProperties(Properties myProperties) {
        this.myProperties = myProperties;
    }

}
{% endhighlight %}

Here is the two spring beans config for your class and for the Properties bean to be injected.

 {% highlight java linenos %}    
<bean id="myBeanClassInstance" class="com.mypackage.MyBeanClass" autowire="byName"/>

<bean id="myProperties" class="org.springframework.beans.factory.config.PropertiesFactoryBean">
  <property name="location">
    <value>/WEB-INF/classes/myPropertiesFile.properties</value>
  </property>
</bean>
 {% endhighlight %}

In this example the prop **myProperties** of the instance **myBeanClassInstance** reference the properties file **/WEB-INF/classes/myPropertiesFile.properties**.




See ya!
