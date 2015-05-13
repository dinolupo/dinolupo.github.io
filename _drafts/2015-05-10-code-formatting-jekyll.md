---
layout: post
title: "Code Formatting Test"
comments: false
---

{% highlight ruby linenos %}
def show
  puts "Outputting a very lo-o-o-o-o-o-o-o-o-o-o-o-o-o-o-o-ong lo-o-o-o-o-o-o-o-o-o-o-o-o-o-o-o-ong line"
  @widget = Widget(params[:id])
  respond_to do |format|
    format.html # show.html.erb
    format.json { render json: @widget }
  end
end
{% endhighlight %}
<!--more-->
## References

[https://demisx.github.io/jekyll/2014/01/13/improve-code-highlighting-in-jekyll.html](https://demisx.github.io/jekyll/2014/01/13/improve-code-highlighting-in-jekyll.html)
 
