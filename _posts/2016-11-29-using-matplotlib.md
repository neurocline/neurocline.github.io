---
layout: post
title:  "Using Matplotlib"
date:   2016-11-29 1:11:00 PM
categories: dev
tags: python
---

Matplotlib is worth using, but it's very finicky, and the defaults are aimed at
a class of user that doesn't include me.

## MATLAB style with pyplot

I started with the simple declarative style via `matplotlib.pyplot`, but that quickly
gets limited. It looks like this:

{% highlight python %}
import matplotlib
import numpy
matplotlib.use('Agg')
matplotlib.rcParams['lines.linewidth'] = 2
matplotlib.rcParams['lines.color'] = 'r'
matplotlib.rcParams['figure.figsize'] = (6,3)
import matplotlib.pyplot as plt
plt.title('Orgs by create date')
plt.xlabel('date')
plt.ylabel('count')
d = []
for r in org_list:
    d.append(year_fraction(r['created_at']))
ax = plt.gca()
ax.ticklabel_format(useOffset=False)
n = numpy.arange(len(d))
plt.plot(d, n)
plt.savefig(plot_name, bbox_inches='tight')
{% endhighlight %}

For example, without the ``useOffset=False`` bit, my X axis was numbered 0-5 with
an offset written above it, rathern than 2012-2017 as I intended. I suppose this is
useful for scientific plots. I spent 30 minutes combing through documentation trying
to figure this out before resorting to StackOverflow. Similarly, I had to use other
StackOverflow entries to tell me how to write the graph as a file and not to show
it in an interactive window. Again, I'm not the target audience.

## object-oriented Matplotlib

This is the right way for me, I think. Note that the code example above had to dip
into the object layer anyway, in order to configure the X axis.

## xkcd style

One cool thing is that someone made an xkcd style for Matplotlib. Just wrap your
code in this:

{% highlight python %}
with matplotlib.pyplot.xkcd():
    # your code here
{% endhighlight %}

and your graphics will have that hand-drawn xkcd style applied to them.

Here is the example from the Matplotlib docs

{% highlight python %}
import matplotlib.pyplot as plt
import numpy as np

with plt.xkcd():
    # Based on "Stove Ownership" from XKCD by Randall Monroe
    # http://xkcd.com/418/

    fig = plt.figure()
    ax = fig.add_axes((0.1, 0.2, 0.8, 0.7))
    ax.spines['right'].set_color('none')
    ax.spines['top'].set_color('none')
    plt.xticks([])
    plt.yticks([])
    ax.set_ylim([-30, 10])

    data = np.ones(100)
    data[70:] -= np.arange(30)

    plt.annotate(
        'THE DAY I REALIZED\nI COULD COOK BACON\nWHENEVER I WANTED',
        xy=(70, 1), arrowprops=dict(arrowstyle='->'), xytext=(15, -10))

    plt.plot(data)

    plt.xlabel('time')
    plt.ylabel('my overall health')
    fig.text(
        0.5, 0.05,
        '"Stove Ownership" from xkcd by Randall Monroe',
        ha='center')

    # Based on "The Data So Far" from XKCD by Randall Monroe
    # http://xkcd.com/373/

    fig = plt.figure()
    ax = fig.add_axes((0.1, 0.2, 0.8, 0.7))
    ax.bar([-0.125, 1.0 - 0.125], [0, 100], 0.25)
    ax.spines['right'].set_color('none')
    ax.spines['top'].set_color('none')
    ax.xaxis.set_ticks_position('bottom')
    ax.set_xticks([0, 1])
    ax.set_xlim([-0.5, 1.5])
    ax.set_ylim([0, 110])
    ax.set_xticklabels(['CONFIRMED BY\nEXPERIMENT', 'REFUTED BY\nEXPERIMENT'])
    plt.yticks([])

    plt.title("CLAIMS OF SUPERNATURAL POWERS")

    fig.text(
        0.5, 0.05,
        '"The Data So Far" from xkcd by Randall Monroe',
        ha='center')

plt.show()
{% endhighlight %}

## ggplot

I wish Matplotlib learned more lessons from Leland Wilkinson's Grammar of Graphics.

[Seaborn](http://seaborn.pydata.org/index.html#) is an interesting start.

# Reference

[Matplotlib yaxis range display using absolute values rather than offset values?](http://stackoverflow.com/questions/9303728/matplotlib-yaxis-range-display-using-absolute-values-rather-than-offset-values)

[matplotlib: format axis offset-values to whole numbers or specific number](http://stackoverflow.com/questions/3677368/matplotlib-format-axis-offset-values-to-whole-numbers-or-specific-number)

[A Dramatic Tour through Python’s Data Visualization Landscape (including ggplot and Altair)](https://dansaber.wordpress.com/2016/10/02/a-dramatic-tour-through-pythons-data-visualization-landscape-including-ggplot-and-altair/)

[ggplot for python](http://blog.yhat.com/posts/ggplot-for-python.html)

[Matplotlib and the Future of Visualization in Python](https://jakevdp.github.io/blog/2013/03/23/matplotlib-and-the-future-of-visualization-in-python/)

[Exploratory Data Analysis](https://www.amazon.com/Exploratory-Data-Analysis-John-Tukey/dp/0201076160)

[Semiology of Graphics: Diagrams, Networks, Maps](https://www.amazon.com/Semiology-Graphics-Diagrams-Networks-Maps/dp/1589482611)
