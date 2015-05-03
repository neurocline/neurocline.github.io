---
layout: post
title:  "Font Awesome"
date:   2015-05-02 22:56:00
categories: html blog
---
![My helpful screenshot](/assets/FontAwesome.jpg)

This is cool

[Font Awesome](http://fortawesome.github.io/Font-Awesome/)

This is an open-source and free icon font. It's pretty easy to use, you add
the font-awesome directory to your project, reference the font-awesome stylesheet
from your page, and then just start adding characters in the font.

It works well with Bootstrap 3, and you can either integrate Bootstrap yourself,
or start with the Jekyll-Bootstrap project. Or, just hand-add CSS and HTML
yourself.

I added this to my ```_includes/head.html``` template file:

{% highlight html %}
{% raw %}
<link rel="stylesheet" href="{{ "/font-awesome/css/font-awesome.min.css" | prepend: site.baseurl }}">
{% endraw %}
{% endhighlight %}

Here's a corny yet effective example:

<i class="fa fa-refresh fa-4x fa-spin"></i>

{% highlight html %}
<i class="fa fa-refresh fa-4x fa-spin"></i>
{% endhighlight %}

I hope to have fun with this, in tasteful effective ways.

## Reference

[Font Awesome Icons](http://fortawesome.github.io/Font-Awesome/icons/)
