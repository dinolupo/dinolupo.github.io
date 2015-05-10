---
author: dinolupo
comments: true
date: 2006-09-23 14:35:45+00:00
layout: post
slug: replaceregexp-use-ant-to-modify-properties-in-configuration-files-xml-properties-etc-part-1-xml-tag-matching
title: 'Replaceregexp: use ANT to modify properties in configuration files (XML, properties,
  etc.) - Part 1 - XML tag matching'
wordpress_id: 22
categories:
- ANT
---

  
Enterprise web applications have multiple configuration files both XML and properties types. When you deploy your application you may need to change values like URL, IP, login, password, etc. in all of those files. My solution was to build a tool with ANT using "replaceregexp". But let's go with an example.
<!--more-->
We have a JBoss data source definition file into the deploy directory, this example has two db connections:
        
 {% highlight xml linenos %} 
<datasources>

  <local-tx-datasource>
    <jndi-name>FirsConnectionDS</jndi-name>
    <connection-url>jdbc:mysql://192.168.1.2:3306/database1</connection-url>
    <driver-class>org.gjt.mm.mysql.Driver</driver-class>
    <user-name>root</user-name>
    <password>SamplePass123</password>
    <!-- all other properties are optional -->
    <transaction-isolation>TRANSACTION_READ_COMMITTED</transaction-isolation>
    <!--pooling parameters-->
    <min-pool-size>5</min-pool-size>
    <max-pool-size>100</max-pool-size>
    <blocking-timeout-millis>5000</blocking-timeout-millis>
    <idle-timeout-minutes>15</idle-timeout-minutes>
  </local-tx-datasource>
  
  <local-tx-datasource>
    <jndi-name>SecondConnectionDS</jndi-name>
    <connection-url>jdbc:mysql://dbserver_name:3306/database2</connection-url>
    <driver-class>org.gjt.mm.mysql.Driver</driver-class>
    <user-name>root</user-name>
    <password>SamplePass123</password>
    <!-- all other properties are optional -->
    <transaction-isolation>TRANSACTION_READ_COMMITTED</transaction-isolation>
    <!--pooling parameters-->
    <min-pool-size>5</min-pool-size>
    <max-pool-size>100</max-pool-size>
    <blocking-timeout-millis>5000</blocking-timeout-millis>
    <idle-timeout-minutes>15</idle-timeout-minutes>
  </local-tx-datasource>

<datasources>
{% endhighlight %}


The ant build.xml that permits to modify the connection-url, user-name and passwords is this:  
(to explain the the regular expression which I used, note that I match the entire XML tag and it's content, and with the use of parenthesis I remember what to mantain and what to discard/substitute).  


{% highlight xml linenos %} 
<?xml version="1.0"?>
<project name="datasources_configuration" default="init" basedir=".">

    <!-- ************************************************************ -->
    <!--             Configuration properties                         -->
    <!-- ************************************************************ -->
    
    <property name="jboss" value="c:/jboss-3.2.6"/>
    <property name="srv" value="all"/>
    
    <property name="mysql_db_ip" value="192.168.1.10"/>
    <property name="mysql_db_port" value="3306"/>
    <property name="mysql_db_user" value="root_new"/>
    <property name="mysql_db_password" value="Password_new"/>
    
    <target name="init">
        <echo message="-------------------------------------------------------------------------" />
        <echo message="JBoss directory:             ${jboss}" />
        <echo message="JBoss configuration:         ${srv}" />
        <echo message="MySql server/port:           ${mysql_db_ip}/${mysql_db_port}" />
        <echo message="MySql user/password:         ${mysql_db_user}/${mysql_db_password}" />
        <echo message="-------------------------------------------------------------------------" />
    </target>


    <target name="all_services_db" depends="init">
        <description> All services Data Source configuration task </description>
        <replaceregexp byline="true">
            <regexp pattern="(Wconnection-urlWs*jdbc:mysql://)(.+)/((w+)WWconnection-urlW)"/>
            <substitution expression="1${mysql_db_ip}:${mysql_db_port}/3"/>
            <fileset dir="${jboss}/server/${srv}/deploy" casesensitive="no">
                <include name="standard-mysql-ds.xml"/>
            </fileset>
        </replaceregexp>
        
        <replaceregexp byline="true">
            <regexp pattern="(Wuser-nameWs*)(.+)(WWuser-nameW)"/>
          <substitution expression="1${mysql_db_user}3"/>
            <fileset dir="${jboss}/server/${srv}/deploy" casesensitive="no">
                <include name="standard-mysql-ds.xml"/>
            </fileset>
        </replaceregexp>
        <replaceregexp byline="true">
            <regexp pattern="(WpasswordWs*)(.*)(WWpasswordW)"/>
          <substitution expression="1${mysql_db_password}3"/>
            <fileset dir="${jboss}/server/${srv}/deploy" casesensitive="no">
                <include name="standard-mysql-ds.xml"/>
            </fileset>
        </replaceregexp>
        
    </target>

</project>
{% endhighlight %}

Only thing you have to do is to set your property at the top of the build.xml file and then run it with ANT.  


See ya!
