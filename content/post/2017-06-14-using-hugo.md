+++
date = "2017-06-14T15:27:58-07:00"
title = "Using Hugo for a blog"

+++

![My helpful screenshot](/assets/HugoBlogging.jpg)

It's time to switch; I'm tired of Jekyll. It's actually holding me back from writing more, as trivial as
that sounds. Enter Hugo.

- [Hugo](http://gohugo.io/)

It's written in Go, so it's just a binary, no Ruby or Python or Perl runtime needed. It claims to be really
fast. The easiest approach is to install a binary version from releases on Github:

- [gohugoio/hugo](https://github.com/gohugoio/hugo/releases)

However, you can easily build from source, as long as you have a recent Go version.

- [The Go Programming Language](https://golang.org/)
- [Hugo source](https://github.com/gohugoio/hugo)

I am a little surprised by just how much source code comprises Hugo. But there are cool things in it,
so it's just what it takes, I guess.

## Make a new site.

```
$ hugo new site neurocline-hugo
```

We need a theme; let's start with something really minimal.

```
$ git clone git@github.com:Zenithar/hugo-theme-bleak.git
```

## Create a new post

```
$ hugo new post/first.md
```

and edit the file `content/post/first.md` to contain something. In fact, make it contain this text.

Now run a local server to preview.

```
$ hugo server --theme=hugo-theme-bleak --buildDrafts
```

It takes a whopping 7 milliseconds to generate and start serving content; the `hugo server` command also watches
the directory tree being served and regenerates when it sees changes, and this happens quickly as well. This
is described in more detail in the [Hugo Quickstart Guide](http://gohugo.io/overview/quickstart/).

## Import existing Jekyll site

So far so good. What about lots of content? There is a Jekyll importer

- [hugo import jekyll](http://gohugo.io/commands/hugo_import_jekyll/)
- [Migrate to Hugo from Jekyll](http://gohugo.io/tutorials/migrate-from-jekyll/)

Run the initial import. Since I'm not crazy, I created a new site instance to try this with

```
$ hugo new site neurocline-temp
$ hugo import jekyll neurocline.github.io
```

Oops, I guess the `hugo import` command wants to create a new site; makes sense.

```
$ rmdir /s /q neurocline-temp
$ hugo import jekyll neurocline.github.io
```

This died partway through; apparently, I have bad metadata in some of my posts. Set this aside to file a bug
report (or even fix it myself), then fix metadata and continue.

After the import, there's cleanup that might need to be done. In my case, very little, because I didn't use
Jekyll templates other than for code syntax highlighting, and the `hugo import jekyll` did the minimal
conversion needed to turn Liquid templates into Hugo shortcodes. It moved all my static data properly, and
I wasn't using any plugins.

Actually, I'm not sure that the hugo jekyll importer converted my code syntax highlighting sections; I see
lots of issues. I'll have to go through all the posts by hand after all.

I still have a number of things to fix, mostly from posts that were directly referencing unicode characters.

```
ERROR 2017/06/14 16:15:31 post\2017-04-03-type-traits.md:17: unrecognized character in shortcode action: U+002B '+'. Note: Parameters with non-alphanumeric args must be quoted
ERROR 2017/06/14 16:15:31 post\2017-03-31-writing-std-filesystem.md:34: unrecognized character in shortcode action: U+002B '+'. Note: Parameters with non-alphanumeric args must be quoted
ERROR 2017/06/14 16:15:31 post\2017-04-03-writing-std-filesystem-path-part-2.md:24: unrecognized character in shortcode action: U+002B '+'. Note: Parameters with non-alphanumeric args must be quoted
```

Initial site generation took 12 seconds. I thought Hugo was fast? Hmm, the next time it only took 146 milliseconds.

OK, that's fair that it does caching, but it puts a new spin on Hugo's speed. Sure, scanning through a page that has
already been generated takes a millisecond, but generating a new page from scratch takes around 100 milliseconds.
And that does not seem fast. Something to look into. My expectation would be that a live rebuild takes almost no
time.

## Good to go

But Hugo is orders of magnitude faster than Jekyll, and I think I'll have an easier time beating it into shape. Certainly,
this means I don't need Ruby in the picture, which is good (for me).

This is good enough to switch to right now. So I did.

## More to do

The `hugo import jekyll` converter doesn't create any layout information. And in looking through what to do, I'm not all that
impressed with the Hugo documentation. After some reading, I found a particularly good tutorial that explained a lot of Hugo's
design. Theme choices affect how you organize your site, meaning that you can't necessarily arbitrarily switch themes. There
are some standards that have evolved over the past few years, I just need to learn what they are.

The other issue is that in order to get the look I want, I have to get better with HTML and CSS. But that was true with Jekyll as
well.

As a contrary note, Docker switched from Hugo to Jekyll. Maybe it was for better interop with Github?

- [ANNOUNCING THE NEW DOCS REPO ON GITHUB](https://blog.docker.com/2016/09/announcing-new-docs-repo-github/)

I have to agree that none of these systems seem ideal for documentation, especially if you want documentation
to be revisioned (which seems important). However, reading the above by Docker doesn't seem like an inevitable
solution, but just one of many.

# Reference

[Generate GitHub pages in a submodule](http://blog.blindgaenger.net/generate_github_pages_in_a_submodule.html)

[Building a theme with Hugo](http://www.humboldtux.net/sbcb-demo/post/post-01/)

[DocuAPI is a beautiful multilingual API documentation theme for Hugo](https://github.com/bep/docuapi/)

[Static Site Generators Reviewed: Jekyll, Middleman, Roots, Hugo](https://www.smashingmagazine.com/2015/11/static-website-generators-jekyll-middleman-roots-hugo-review/)

[WrapBootstrap](https://wrapbootstrap.com/)

[Victor Hugo](https://github.com/netlify/victor-hugo)

Example clean Hugo site

- [George Ornbo](https://shapeshed.com/)
- [shapeshed/shapeshed.com](https://github.com/shapeshed/shapeshed.com)
- [shapeshed/shapeshed-minimal](https://github.com/shapeshed/shapeshed-minimal)
- [Gradient Ascent](http://navinpai.github.io/ga/)
- [AlexFinn/simple-a](https://github.com/AlexFinn/simple-a)

[wp export](https://developer.wordpress.org/cli/commands/export/)

# Bugs to fix

This post caused the jekyll importer to fail with an error

```
ERROR 2017/06/14 16:05:35 Convert metadata error: C:\projects\github\neurocline.github.io\_posts\2015-11-13-electronic-music-bands.markdown
Error: unable to cast <nil> of type <nil> to map[string]interface{}
```

It is missing the initial `---` text.

```
layout: post
title:  "Electronic bands to listen to"
date:   2015-11-13 15:29:00 PM
categories: music
tags: electronic
---
```
