---
layout: post
title: "Java EE 7: SSL Security on Wildfly 10"
comments: true
categories: javaee, jee, security, jboss, wildfly
---

We are going to secure both the management interface and the application access of Wildfly 10 <!--more-->

### Securing WildFly 10 with SSL 

1) Create a key pair

Open a terminal window in the folder {jboss.home}/standalone/configuration and enter the following command:

{% highlight sh %}
keytool -genkey -alias server -keyalg RSA -keystore server.keystore -validity 365
{% endhighlight %}

{% highlight sh %}
Enter keystore password:
Re-enter new password:
{% endhighlight %}

In this example I choose 'keystore_password'.

{% highlight sh %}
What is your first and last name?
  [Unknown]:  localhost
{% endhighlight %}

2) Set the server keystore for both `ManagementRealm` and `ApplicationRealm`:

{% highlight xml %}
...
<security-realm name="ApplicationRealm">
<server-identities>
	<ssl>
		<keystore path="server.keystore" relative-to="jboss.server.config.dir" keystore-password="keystore_password" alias="server" key-password="key_password"/>
    </ssl>
</server-identities>
...
{% endhighlight %}


3) To secure the management interface you will have to change the socket binding into https like the following:

{% highlight xml %}
<management-interfaces>
	<http-interface security-realm="ManagementRealm" http-upgrade-enabled="true">
    	<socket-binding https="management-https"/>
    </http-interface>
</management-interfaces>
{% endhighlight %}


4) To secure the applications you have to add an `https-listener`. If you want to disable HTTP then delete it:

{% highlight xml %}
...
<subsystem xmlns="urn:jboss:domain:undertow:3.0">
    <buffer-cache name="default"/>
    <server name="default-server">
        <http-listener name="default" socket-binding="http" redirect-socket="https"/>
        <https-listener name="defaultssl" security-realm="ApplicationRealm" socket-binding="https"/>
        <host name="default-host" alias="localhost">
            <location name="/" handler="welcome-content"/>
            <filter-ref name="server-header"/>
            <filter-ref name="x-powered-by-header"/>
        </host>
    </server>
...
{% endhighlight %}

### Difference between security-realm and security-domain in WildFly




### References

[http://stackoverflow.com/questions/32008182/wildfly-9-http-to-https]()

[https://docs.jboss.org/author/display/WFLY10/Security+Realms]() find Enable SSL section

[http://stackoverflow.com/questions/31036090/difference-between-security-realm-and-security-domain-in-wildfly]()







