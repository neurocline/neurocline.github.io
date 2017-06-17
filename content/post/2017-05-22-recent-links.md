---
categories: dev
date: 2017-05-22T02:45:00Z
tags: text
title: Recent links
url: /2017/05/22/recent-links/
---

[Sustainability of Digital Formats: Planning for Library of Congress Collections](https://www.loc.gov/preservation/digital/formats/fdd/browse_list.shtml)

MBOX is the de-facto standard for archiving email.

- [LOC: MBOX](https://www.loc.gov/preservation/digital/formats/fdd/fdd000383.shtml)
- [RFC 4155](https://tools.ietf.org/rfc/rfc4155.txt)
- [qmail mbox description](http://qmail.org/qmail-manual-html/man5/mbox.html)

[The “What” of Email Archiving](https://blogs.loc.gov/thesignal/2013/07/the-what-of-email-archiving/)

Git grafts is old but still exists

- https://git.wiki.kernel.org/index.php/GraftPoint
- [Git: Grafting repositories](http://ben.straubnet.net/post/939181602/git-grafting-repositories)
- [Collating repositories or grafting earlier history with Git](https://developer.atlassian.com/blog/2015/08/grafting-earlier-history-with-git/)
- [How do git grafts and replace differ? (Are grafts now deprecated?)](https://stackoverflow.com/questions/6800692/how-do-git-grafts-and-replace-differ-are-grafts-now-deprecated)

`git replace` is the newer mechanism, and can be pushed to others (grafts are local-only)

- https://git-scm.com/docs/git-replace

`git filter-branch` will flatten any entries in `.git/info/grafts` and `refs/replace/`, making them permanent.
This is a useful technique to join history together.

[The power of Git subtree](https://developer.atlassian.com/blog/2015/05/the-power-of-git-subtree/)

In particular, `git subtree` is distinct from subtree merges. I'm now more enlightened, although I need to experiment.

[Using Git subtrees for repository separation](https://makingsoftware.wordpress.com/2013/02/16/using-git-subtrees-for-repository-separation/)
