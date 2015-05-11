---
layout: post
title:  "Fun and useful HTML Entities"
date:   2015-05-02 21:40:00
categories: html blog
---
![My helpful screenshot](/assets/HtmlEntities.jpg)

# SGML &rarr; HTML &rarr; XML &rarr; HTML5

HTML Entities are still necessary in some cases, and useful all around.

Some characters can't be represented directly in HTML - the '<' character,
for example, opens an HTML tag, so if you want this in HTML, you need to
use the HTML entity ```&lt;```. Other characters are impossible to type
on some keyboards, or only draw in specific fonts: the '&rarr;' character,
which is the HTML entity ```&arr;```. Diacritical marks are another
reason to use entities.

HTML entities have names that you can memorize. However, each HTML entity
has an Entity Number, and this is, not coincidentally, the Unicode
codepoint for the character. You can write HTML entities in decimal, or
you can write them in hexadecimal, which is how Unicode codepoints are
usually documented.

Here's my list of useful HTML Entities. The first 3 are the reserved
characters where you must use the HTML entity, the rest are just awkward
or impossible to type. And strictly speaking, '>' is not reserved, it's
just best to treat it that way, because there are many cases where it must
be written as an entity.

Character | Entity-name | Entity-number | Description
--------- | ----------- | ------------- | ------------
&amp;     | &amp;amp;   | &amp;#38;     | ampersand
&lt;      | &amp;lt;    | &amp;#60;     | less-than
&gt;      | &amp;gt;    | &amp;#62;     | greater-than
&nbsp;    | &amp;nbsp;  | &amp;#160;    | non-breaking space
&larr;    | &amp;larr;  | &amp;#8592;   | leftwards arrow
&rarr;    | &amp;rarr;  | &amp;#8594;   | rightwards arrow
&eacute;  | &amp;eacute;| &amp;#233;    | latin e with acute

## HTML Entities you should not use

Because many web developers were American or English, and because early
HTML support was somewhat primitive, the habit of using unusual characters
as separators developed.

Don't do it. Here's a list of characters you should avoid using except
for their actual purpose.

### Guillemets

Characters: &lsaquo; &rsaquo; &laquo; &raquo;

These are punctuation marks in languages like French. However, they are often
misused either as button labels for forward and backward navigation, or as
separator characters in a breadcrumb navigation list.

A French reader would use the latter two quotes to indicate speech:

&laquo;&nbsp;Bonjour, Sally.&nbsp;&raquo; dit Charles.

Imagine how confusing it is to see your quote marks used as some mysterious
and arcane symbol in a list. Don't do it. Use arrow characters like &larr; and
&rarr; instead, or some other appropriate character. Or, use SVG or icons.

Fun trivia fact: Adobe originally got this wrong and called these guillemot.
Unfortunately, a guillemot is a species of seabird. Adobe noted their error
in the Postscript Language Reference, 3rd edition, 1999, but the harm was done,
and many English-speakers still refer to these by the incorrect name.

## Reference

[HTML Entities (w3schools)](http://www.w3schools.com/html/html_entities.asp)

[Character Entity Reference Chart](http://dev.w3.org/html5/html-author/charref)

[Unicode character reference](http://en.wikibooks.org/wiki/Unicode/Character_reference/0000-0FFF)

[List of Unicode characters](http://en.wikipedia.org/wiki/List_of_Unicode_characters) from Wikipedia.

[Unicode 7.0 Character Code Charts](http://unicode.org/charts/)

[Unicode table](http://unicode-table.com/en/)

[HTML Entities (danshort)](http://www.danshort.com/HTMLentities/)
