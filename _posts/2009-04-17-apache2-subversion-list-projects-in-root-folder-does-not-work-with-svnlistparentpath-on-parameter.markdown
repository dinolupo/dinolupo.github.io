---
author: dinolupo
comments: true
date: 2009-04-17 08:46:47+00:00
layout: post
slug: apache2-subversion-list-projects-in-root-folder-does-not-work-with-svnlistparentpath-on-parameter
title: Apache2 Subversion list projects in root folder does not work with SVNListParentPath
  on parameter.
wordpress_id: 10
categories:
- Linux
---

How to list projects in apache2 - subversion (if "SVNParentPathList on" does not work).  
<!--more-->  
I tried this on apache2 - subversion 1.4.6  
  
1) If you don't have php, enable it doing the following: 
    
{% highlight sh linenos %}  
sudo apt-get install php5 
sudo a2enmod php5 
sudo /etc/init.d/apache2 force-reload 
sudo /etc/init.d/apache2 restart
{% endhighlight %}

2) Add the file svn_index.php in the web root folder (mine is at /var/www) with the following content:


{% highlight php linenos %}
<html>
<head>
<title>Subversion Repositories</title>
</head>
<body>

<h2>Subversion Repositories</h2>
<p>
<?php
    $svnparentpath = "/svn";
    $svnparenturl = "/svn";

    $dh = opendir( $svnparentpath );
    if( $dh ) {
        while( $dir = readdir( $dh ) ) {
            $svndir = $svnparentpath . "/" . $dir;
            $svndbdir = $svndir . "/db";
            $svnfstypefile = $svndbdir . "/fs-type";
            if( is_dir( $svndir ) && is_dir( $svndbdir ) ) {
                echo "<a href="" . $svnparenturl . "/" .
                        $dir . "">" . $dir . "</a>n";
                if( file_exists( $svnfstypefile ) ) {
                    $handle = fopen ("$svnfstypefile", "r");
                    $buffer = fgets($handle, 4096);
                    fclose( $handle );
                    $buffer = chop( $buffer );
                    if( strcmp( $buffer, "fsfs" )==0 ) {
                        echo " (FSFS) <br />n";
                    } else {
                        echo " (BDB) <br />n";
                    }
                } else {
                    echo " (BDB) <br />n";
                }
            }
        }
        closedir( $dh );
    }
?>
</p>

</body>
</html> 
{% endhighlight %}



bear in mind that in the code I assume that the root path of svn ($svnparentpath) is /svn (if you are in windows write C:svn instead).


3) create a symbolic link to enable mod rewriting:

{% highlight sh linenos %}  
sudo ln –s /etc/apache2/mods-available/rewrite.load /etc/apache2/mods-enabled/rewrite.load
{% endhighlight %}



4) Add the following lines to /etc/apache2/mods-enabled/dav_svn.conf just below the <Location /svn/> tag (my root repository url is /svn/):

{% highlight sh linenos %}  
RewriteEngine on
RewriteRule ^/svn$ /svn_index.php [PT]
RewriteRule ^/svn/$ /svn_index.php [PT]
RewriteRule ^/svn/index.html$ /svn_index.php [PT] 
{% endhighlight %}



  
5) Restart Apache2

    
    <span style="color:#000000;">sudo /etc/init.d/apache2 restart</span>
