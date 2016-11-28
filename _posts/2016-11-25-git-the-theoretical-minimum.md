---
layout: post
title:  "Git: The Theoretical Minimum"
date:   2016-11-25 1:33:00 PM
categories: dev
tags: git
---

I intend to write a book titled "Git: The Theoretical Minimum", inspired by Leonard Susskind's
two books [The Theoretical Minimum: What You Need to Know to Start Doing Physics](https://www.amazon.com/Theoretical-Minimum-Start-Doing-Physics/dp/0465075681)
and [Quantum Mechanics: The Theoretical Minimum](https://www.amazon.com/Quantum-Mechanics-Theoretical-Leonard-Susskind/dp/0465062903)
(also see his web site [The Theoretical Minimum](http://theoreticalminimum.com/)). These
are awesome books and I recommend them to anyone, because physics is all around us and
having a working knowledge of it is a good thing.

My reasoning in a nutshell is this: Git is the first real version control system, or rather
it is probably the ancestor of the first real one. And while it is not as complicated to use
as detractors like to say, it's also not been explained in a succinct fashion. There are now
many thousands of pages written, and yet a lot of Git is not explained except in answers
to StackOverflow questions or by looking at the source.

I think this means that there is a need for a short volume that explains everything you need
in order to do anything with Git, and I also think that this overlaps with "everything about
version control" to a staggering degree. I don't mean to have the encyclopedic volume that
literally writes down everything, but rather the bare minimum you need in order to do anything
you need to on your own. For example, the Git help is pretty good, but it's also only good if
you already understand things - it's more a reminder reference than something you consult in
order to learn what to do.

For example, it is probably not important to describe the Git plumbing model, but it is
important to describe exactly how commits work, because that's at the heart of many Git
operations. What I hope to do is to find the basic set of things you need to know about
any useful version control system, and describe them; the fact that it will be Git-centric
is because Git is the most popular example of a good version control system at the moment.

Separately, I want to finish the survey of all version control systems, so that we can put
most of them to bed as past history. "I have not come here to praise Perforce but to bury it".

# Reference

[Mercurial for Git Users (and vice versa)](https://www.rath.org/mercurial-for-git-users-and-vice-versa.html) - a more modern (early 2016) comparison between
Mercurial and Git.

[What are your favorite version control systems?](http://softwareengineering.stackexchange.com/questions/940/what-are-your-favorite-version-control-systems) - I remember using both PVCS and Projector.

[Comparison of version control software](https://en.wikipedia.org/wiki/Comparison_of_version_control_software)

[Top Version Control Systems](http://www.improgrammer.net/top-version-control-systems/)

[Version Control Systems Popularity in 2016](https://rhodecode.com/insights/version-control-systems-2016)

[Version Control Systems: Keep Your Code In Order!](https://webinerds.com/version-control-systems-keep-your-code-in-order/)

[Understanding Version-Control Systems (DRAFT)](http://www.catb.org/esr/writings/version-control/version-control.html) - written in 2008, and may be the first attempt to cover the general theory of revision control.

[Version control](https://en.wikipedia.org/wiki/Version_control)

[Source Control HOWTO](http://ericsink.com/scm/source_control.html) - from Eric Sink, useful for common terminology.

[Version Control by Example](http://ericsink.com/vcbe/html/index.html) - a 2011 online book by Eric Sink.

[A Visual Guide to Version Control](https://betterexplained.com/articles/a-visual-guide-to-version-control/)

[Intro to Distributed Version Control (Illustrated)](https://betterexplained.com/articles/intro-to-distributed-version-control-illustrated/)

[A Principled Approach to Version Control](http://cs.ru.nl/~wouters/Publications/PrincipledVersionControl.pdf) - a 2006 paper: "This paper aims to give a precise, mathematical description of the version control problem and different version control systems"

[The Semantics of Version Control](http://www.staff.science.uu.nl/~swier004/publications/2014-onward.pdf). A 2014 paper by the authors of the previous paper.

[Pijul](https://pijul.org/), based on Darcs. Pijul was announced in 2014 but it's still in (mostly slow moving) development. See [HackerNews comment thread](https://news.ycombinator.com/item?id=10470281).

[Darcs: Theory/Bibliography](http://darcs.net/Theory/Bibliography). Darcs is one the few systems that claims to be based on a strong theory; in fact, Darcs devotees are often dismissive of other efforts as not having a formal backdrop. Darcs vs Git is a classic example of "it works in theory"; to two significant digits, no one uses Darcs, everyone uses Git.

[Camp (Haskell)](http://projects.haskell.org/camp/). Another system ideologically related to Darcs. Start in 2009, and seems to have been abandoned as of 2010.

[Current Concepts in Version Control Systems](http://pasky.or.cz/bc/bcthesis-final.pdf). 2008 Bachelor thesis by Petr Baudiˇs.

[Homotopical Patch Theory](https://www.cs.cmu.edu/~rwh/papers/htpt/paper.pdf)

[Math talk: Theorem about git revision control system?](http://cstheory.stackexchange.com/questions/32374/math-talk-theorem-about-git-revision-control-system)

[Version Control Systems](https://rahul.gopinath.org/post/2011/12/30/version-control-systems/). The best historical overview I've seen to date.

[Branching and Merging: An Investigation into Current Version Control Practices](http://citeseerx.ist.psu.edu/viewdoc/download?doi=10.1.1.278.6065&rep=rep1&type=pdf). 2011 paper.

[Distributed Source Code Version Control Systems: Comparison and Usage Evaluation](http://is.muni.cz/th/98961/fi_b/BcThesis.pdf). 2006 Bachelor's thesis by Michal M¥rka.

[Type-Correct Changes — A Safe Approach to Version Control Implementation](https://files.codersbase.com/thesis.pdf). 2009 Master's Thesis by Jason Dagit.

[Version Control: Git, Mercurial](http://hyperpolyglot.org/version-control)

[TAOSA: Mercurial](http://www.aosabook.org/en/mercurial.html).

[BZR INIT: A BAZAAR TUTORIAL](https://duckrowing.com/2013/12/26/bzr-init-a-bazaar-tutorial/)

[A Visual Git Reference](http://marklodato.github.io/visual-git-guide/index-en.html)

[WHY I LIKE MERCURIAL MORE THAN GIT](https://jhw.dreamwidth.org/1868.html)

[On Mercurial](http://schacon.github.io/2008/11/24/on-mercurial.html)

[Mercurial for Git users](https://www.mercurial-scm.org/wiki/GitConcepts)
