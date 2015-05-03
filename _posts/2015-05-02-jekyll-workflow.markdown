---
layout: post
title:  "A Jekyll Workflow"
date:   2015-05-02 19:15:00
categories: jekyll blog
---
![My helpful screenshot](/assets/JekyllWorkflow.jpg)

This is my current Jekyll workflow.

Since I update my blog from multiple computers, the first step is to sync with latest from GitHub:

{% highlight text %}
> cd neurocline.github.io
> git fetch --all
> git rebase origin/sources sources
> cd _site
> git rebase origin/master master
> cd ..
{% endhighlight %}

Yes, I almost always fetch instead of pull, because if I had forgotten I'd made local changes, I
don't want a merge commit (even true when working in a group). That's the reason for the rebase; I
could do ```git merge --ff-only ...``` instead, which would either do a fast-forward or stop if
there are changes that need to be merged.

Now that I'm up to date, I need to run a local jekyll server so I can preview what I'm doing:

{% highlight text %}
> jekyll serve --drafts
{% endhighlight %}

I use ```--drafts``` because I invariably have one or more draft articles, and I won't see them
otherwise.

Now I edit posts. Currently I just do it the "hard" way, by typing everything; no help from rake
or the like. I expect I'll get fancier as I write more.

When I'm done for a bit, I stop the jekyll server, bake what I have, and commit:

{% highlight text %}
[ctrl-C to stop server]
> jekyll build
> git add -A
> git commit -m "First cut of workflow post"
> git push origin sources
> cd _site
> git add -A
> git commit -m "First cut of workflow post"
> git push origin master
> cd ..
{% endhighlight %}

And since all of this is repetitive, I'll soon look into one of the systems that automates this
a little (I've seen hints of this), or I'll write something myself.
