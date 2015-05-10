---
author: dinolupo
comments: true
date: 2007-12-11 14:53:36+00:00
layout: post
slug: web-service-client-code-generation-with-jaxb
title: web service client code generation with JAXB
wordpress_id: 14
categories:
- Java - J2EE
---

If you used Xfire or JBossWS to create client code to call a web service and used [JAXB](https://jaxb.dev.java.net/) bindings, it was probably very annoying to see all the generated classes having properties as generics (ie. `**JAXBElement**<**String**>` property instead of just `**String**`). This happens because the generator doesn’t handle the _minoccurs=”0”_ attribute properly (especially along with the _nillable=”true”_ attribute) when it is found in the WSDL for the definition of types.
<!--more-->

	

Many people solved this by simply editing the WSDL by hand and modifying the _minoccurs=”0”_ attribute into _minoccurs=”1”_
which then made the generator work properly. This, however has impact
on maintainability of the code and future changes and it is not
recommended.


	

Others approached the problem by simply
unpacking the JARs that come with XFire distribution and manually
modifying the generator configuration. This also makes the deployment
somewhat difficult unless you’re packing the xfire distribution along
with your project.


	

However, the solution can be even simpler. JAXB code generator supports defining how the JAXB binding classes will be created through a XML configuration file. The configuration attribute we’re looking for is under _globalBindings_ directive and it is called _generateElementProperty_ – which is by default **true**. If set to **false**, it will direct the JAXB code generator NOT to generate the properties as **JAXBElement** generics, so it will use the simple types instead.


The external bindings file can look as simple as this:

    
{% highlight xml linenos %}
<jxb:bindings version="2.0" 
              xmlns:jxb="http://java.sun.com/xml/ns/jaxb" 
              xmlns:xs="http://www.w3.org/2001/XMLSchema">
        <jxb:globalBindings generateElementProperty="false" />
</jxb:bindings>
{% endhighlight %}

(more information about this problem and the solution with bindings can be found [here](https://wsit-docs.dev.java.net/releases/m5/DataBinding5.html))

The amount of fine-tuning the generator using the external Bindings file is extremely wealthy. Make sure to check the [JAXB documentation](https://jaxb.dev.java.net/nonav/2.0/binding-customization/) for more details.

(JbossWS) - Save the file and use the option -b on WSConsume, or if you use ANT you have to set the binding option.  
  
Happy new year!  


  


  
  

