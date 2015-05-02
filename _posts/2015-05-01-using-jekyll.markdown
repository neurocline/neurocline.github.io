---
layout: post
title:  "Using Jekyll for a blog"
date:   2015-05-01 18:53:20
categories: jekyll blog
---
![My helpful screenshot](/assets/JekyllBlogging.jpg)

I've been unhappy with Wordpress for a while now - it's slow, it's cumbersome to add content,
and it's riddled with insecurities. Despite not being a "web programmer", I've decided it's time
to go closer to the bare metal - and a blog is much more suited to a static site. This means I can
keep my blog in source control, which makes me much happier than using some CMS system. I decided
to try Jekyll, instead of writing my own. I may still write my own eventually; that's what all
engineers seem to do, over time, to exert the most control.

This has been interesting, because I've also managed to avoid using Ruby all these years; Perl and
then Python are what I'd used for "scripting" languages. I also decided to put it up as a GitHub
Pages site, since I of course was using Git for source control.

So here's the steps I followed, on a Windows machine, just to be contrary; the Web world prefers
Mac and Linux, and normally so do I. But at the time I wanted to restart my blog, I was on a
Windows 7 machine.

## Install Ruby

For Windows, you'll want to use a prebuilt installer from [ruby-installer.org][ruby-installer].
I picked [rubyinstaller-2.1.6][rubyinstaller-2.1.6] because I didn't know what I was doing, and
there was a strong suggestion to use 2.1.x.

I installed a 32-bit build because I've had issues with 64-bit Python and Perl in the past - not
all Perl modules or Python packages support 64-bit. So I just assumed the same for Ruby. Also, I'm
not yet writing large programs, so no need for 64-bit. I installed it into C:/Dev/Ruby21, just
because I try to do a minimal amount of organization.

## Install DevKit

I only found this out after starting to install Jekyll, but Ruby gems that come with native
code need a compiler to build them with. On Linux and Mac, this is automatic because it's almost
always "just there", but for Windows we need compilers. I miss the auto-convenience of Perl and
Python which just found my Visual Studio installs and used them. I couldn't figure out how to
get Ruby to do the same, and I suspect it's hard-coded to use the MinGW suite.

The DevKit download can be found on the [ruby-installer.org][ruby-installer] page. I grabbed
the [DevKit-mingw64-32][ruby-devkit-2.1.0] installer that matched my Ruby install. It's not
really an installer, it's a self-unpacking archive. Unpack it to its permanent home - I have
a C:/Dev folder that I put development tools into, so it went into C:/Dev/RubyDevKit. Don't
let it unpack to its default location, which is into a temp folder.

Once you've unpacked it, you need to install it - I think all this does is write magic entries
into your Ruby folder. The default action should find your Ruby folder, but you can double-check
by looking at config.yml after you run dk.rb init.

{% highlight bash %}
> ruby dk.rb init
> ruby dk.rb install
{% endhighlight %}

Note that on one machine, DevKit didn't install cleanly for some reason - e.g. gem install...
failed with a "install DevKit, please". I rebooted and that didn't fix it, but then I reinstalled
DevKit again (ruby dk.rb install), and this time it worked. I don't know if the reboot was
necessary, but I have a vague memory of being prompted to reboot after installing Ruby on
a different machine. I'll try to reproduce that at some point.

## Install Jekyll

Here's where I diverged from what GitHub was suggesting, for two reasons. One, I just want to
use Jekyll to create my site, and I'll commit the generated site to Git (and push to GitHub).
Two, I had a weird problem when I tried to follow the GitHub instructions, and rather than figure
it out, I went back to my original plan of "have a static site".

First, I installed Jekyll.

{% highlight bash %}
> gem install jekyll
{% endhighlight %}

Then, I created a new Git repository and initialized it as a Jekyll-generated site.

{% highlight bash %}
> mkdir neurocline.github.io
> cd neurocline.github.io
> git init
> jekyll new .
{% endhighlight %}

## Install Pygments or Rouge

Since I'm running Jekyll locally, I had a problem as soon as I started writing Markdown that
used the Liquid syntax highlighting; it didn't work. A few seconds of Googling led me to
understand that I needed more modules.

The gorilla is Pygments, which is Python-based, and has a Ruby wrapper already built in to
Jekyll. Since I am a Python user, this didn't turn me off, so I just installed Pygments. This was
especially easy since I'm using a Python that has pip as part of the core system

{% highlight bash %}
> python -m pip install pygments
{% endhighlight %}

I also had to add a line to my _config.yml file:

{% highlight text %}
# Build settings
markdown: kramdown
highlighter: pygments
{% endhighlight %}

And frustratingly, even though the Pygments page said 'shell' was a style, there is no such
thing and I had to use 'bash'.

## Do local editing

The standard workflow is to run a Jekyll server locally, then start editing content. Every
time you save, the file-watcher will pick up the changes and update the site. By default, your
site is viewed on localhost:4000.

{% highlight text %}
> jekyll serve
Configuration file: C:/projects/github/neurocline.github.io/_config.yml
            Source: C:/projects/github/neurocline.github.io
       Destination: C:/projects/github/neurocline.github.io/_site
      Generating...
                    done.
  Please add the following to your Gemfile to avoid polling for changes:
    gem 'wdm', '>= 0.1.0' if Gem.win_platform?
 Auto-regeneration: enabled for 'C:/projects/github/neurocline.github.io'
Configuration file: C:/projects/github/neurocline.github.io/_config.yml
    Server address: http://127.0.0.1:4000/
  Server running... press ctrl-c to stop.
      Regenerating: 1 file(s) changed at 2015-05-01 19:59:33
...
{% endhighlight %}

## Bake a site

Since I'm not using GitHub's Jekyll, I need to bake the site locally and commit file, then
push to GitHub. And that means a little trickery. Here's one approach.

First, I baked the site:

{% highlight bash %}
> jekyll build
{% endhighlight %}

Then, I pushed the sources for the site to a sources branch. GitHub wants to see the site itself in
a master branch, I want my sources in source control, so I "hide" it in a branch GitHub is not
looking at.

{% highlight bash %}
> git remote add origin git@ghosthub:neurocline/neurocline.git.io
> git checkout -b sources
> git add -A
> git commit -m "First commit of source"
> git push origin sources
{% endhighlight %}

Finally, I pushed the baked site itself to a master branch.

{% highlight bash %}
> cd _site
> echo > .nojekyll
> git init
> git remote add origin git@ghosthub:neurocline/neurocline.git.io
> git add -A
> git commit -m "jekyll first build"
> git push origin master
> cd ..
{% endhighlight %}

At this point, my site is live on GitHub.

If you follow that, it looks a little weird, because I have two different repos both pointing to
the same remote repo. But that's Git for you. This way, I don't need to be copying files from one
place to another. It's a little more extra work for setup, but the steady-state is easy.

## Results

I'm pretty happy with the writing part now. I can edit in text and see results as often as I
want (by saving files and using the default file-watcher behavior). I have yet to add images or
to tweak or change the overall blog style, but that will happen.

## Reference

[Install a Syntax Highlighter](http://jekyll-windows.juthilo.com/3-syntax-highlighting/) from
[Run Jekyll on Windows](http://jekyll-windows.juthilo.com/).

[Pygments](http://pygments.org/)

[Installing DevKit](http://github.com/oneclick/rubyinstaller/wiki/Development-Kit)

[ruby-installer]: http://rubyinstaller.org/downloads/
[rubyinstaller-2.1.6]: http://dl.bintray.com/oneclick/rubyinstaller/rubyinstaller-2.1.6.exe
[ruby-devkit-2.1.0]: http://dl.bintray.com/oneclick/rubyinstaller/DevKit-mingw64-32-4.7.2-20130224-1151-sfx.exe
[ruby-devkit-instr]: http://github.com/oneclick/rubyinstaller/wiki/Development-Kit
