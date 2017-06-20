---
categories: blog
date: 2015-05-02T22:56:00Z
tags:
- html
title: Font Awesome
url: /2015/05/02/font-awesome/
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

```html
<link rel="stylesheet" href="{{ "/font-awesome/css/font-awesome.min.css" | prepend: site.baseurl }}">
```

Here's a corny yet effective example:

<i class="fa fa-refresh fa-4x fa-spin"></i>

```html
<i class="fa fa-refresh fa-4x fa-spin"></i>
```

I hope to have fun with this, in tasteful effective ways.

#### Addendum 2017-06-18

Hmm, this still works in Hugo, and I have no idea why, unless the converter somehow is adding
my font-awesome CSS to the Hugo frontmatter? Hmm, when I look at the generated page, it has
an include to `netdna.bootstrapcdn.com/font-awesome/4.0.3/css/font-awesome.css`, not the
one I had in my site. So it must be the theme I chose that decided to use font-awesome. And
the answer is "yes".

## Reference

[Font Awesome Icons](http://fortawesome.github.io/Font-Awesome/icons/)
