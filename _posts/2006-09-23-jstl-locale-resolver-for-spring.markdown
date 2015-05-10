---
author: dinolupo
comments: true
date: 2006-09-23 11:40:36+00:00
layout: post
slug: jstl-locale-resolver-for-spring
title: JSTL locale resolver for Spring
wordpress_id: 23
categories:
- Java - Spring
---

This locale resolver reads the locale from JSTL configuration. 
<!--more-->
If you need to use the same locale both for Spring and JSTL you have to do the following:

1. Cut-Paste the I18nJstlLocaleResolver class code into your project.  
2. Add the Spring bean "localeResolver" definition to your spring configuration file referencing that class.  
  
Here is the code for the class:

{% highlight java linenos %}     
package dl.spring.utils;

import java.util.Locale;
import javax.servlet.http.HttpServletRequest;
import javax.servlet.http.HttpServletResponse;
import javax.servlet.jsp.jstl.core.Config;

import org.springframework.web.servlet.LocaleResolver;

public class I18nJstlLocaleResolver implements LocaleResolver {

  public Locale resolveLocale(HttpServletRequest request) {
    Locale locale = (Locale) Config.get(request.getSession(), Config.FMT_LOCALE);
        if (locale == null)
        {
            locale = request.getLocale();
        }
        return locale;
  }

  public void setLocale(HttpServletRequest request, HttpServletResponse response, Locale locale) {
    if (locale != null)
      Config.set(request.getSession(),Config.FMT_LOCALE, locale);
  }

}
{% endhighlight %}

And here is the code for the localeResolver bean definition (insert it into your spring definition xml file):

{% highlight xml linenos %} 
<bean id="localeResolver" class="dl.spring.utils.I18nJstlLocaleResolver"/>
{% endhighlight %}


See ya!
