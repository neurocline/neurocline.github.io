---
layout: post
title:  "Using Hugo for a blog"
date:   2017-01-02 11:39:00
categories: blog
tags: [hugo, blog]
---

Near the end of 2012, I started a blog using Wordpress. At the end of 2014, it fell prey to a
hack that inserted hundreds of thousands of spam comments. I'd already been dissatisfied with
Wordpress as being awkward to write in, so I spent no effort to fix it.

In May 2015, I started a new blog using Jekyll. This was somewhat better than using Wordpress;
it went backwards on features, but was a lot easier to write new blog posts, and was a lot faster.
Well, at first. Once I had a hundred blog posts, I noticed Jekyll getting slower and slower, to the
point now that it takes 30 seconds to rebuild. That's annoying. Plus, I hate Liquid, and I'm not
really a Ruby fan.

So, after months of light agonizing, I'm going to switch to Hugo.

[Hugo](https://gohugo.io/)

Big plus is that it's written in Go, which means it's almost as good as writing it in C++, both
speed-wise and the likelihood that I would ever actually add a feature to it.

[Hugo source - Github](https://github.com/spf13/hugo)

Hugo has a Jekyll site importer which I'll be trying out, but I'm not too worried about that part
(and someday I'll import all the content from my older Wordpress blog):

[Migrate to Hugo from Jekyll](https://gohugo.io/tutorials/migrate-from-jekyll/)

There is a showcase of Hugo blogs that I imagine I'll pick through to find an initial template that
I like - while I'm picky on look&feel, it's not to the point where I want to spend a lot of time
writing CSS myself.

[Hugo Site Showcase](https://gohugo.io/showcase/)

That said, I've been enjoying using Sphinx and reStructuredText for writing documentation, and
I'm not really a blogger as in "writing to be read", instead I write to remember or as reference.
I'm ok with using multiple systems, I think.

I expect that whatever I do, I'll use Disqus to have blog comments. Those are always useful.

[Disqus](https://disqus.com/)

I am also considering using Netlify.

[Netlify](https://www.netlify.com)

Also, Netlify has this cool list of static site generators

[StaticGen - Top Open-Source Static Site Generators](https://www.staticgen.com/)

Honorable mention - I toyed with the idea of Nikola, because it was at least written in Python.

[Nikola](https://getnikola.com/)

Another honorable mention is Forestry, which purports to create a CMS from your Jekyll or Hugo blog.
I don't think I need a CMS, but we'll see. However, this does sound useful for non-technical people.

[Forestry](https://forestry.io/)
