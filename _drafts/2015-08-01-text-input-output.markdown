---
layout: post
title:  "Text Input and Output"
date:   2015-08-01 10:40:00 AM
categories: programming
---

Warning: very incomplete! I'm writing notes as I think of them, but the intention
is for a simple and clear and universal way of handling meaningful text input and
output on all (most) platforms.

## Why is this hard?

One of the hard-but-should-be-easy tasks is that of text input and text output.
Text is simple compared to graphics, but people and code founder on the handling
of text because the byte streams have meaning, and we have to encode and decode
to turn byte streams into meaningful symbols.

Starting with the earliest uses of computers to store and manipulate text, the
idea of a code table arose - a mapping between integers and characters. After many
incompatible vendor tables, the ASCII mapping was standardized in 1963. Since virtually
all computing was in English at the time, a generation of programmers grew up not
realizing that this was in fact an arbitrary encoding.

Since ASCII was a 7-bit code, vendors started putting other characters into the
remaining 128 entries in the table. Some put graphic characters, others put characters
from other European countries as computers became more generally used. This latter
caused the proliferation of a number of half-compatible character sets - all respected
the ASCII standard, but added their own characters for their language needs. This made
transporting ASCII text between systems safe, but not transporting text using code
points 128-255.

IBM, with the introduction of the IBM PC, decided to embrace variants. The character
set of the original IBM PC/MS-DOS was called code page 437, reflecting the character
set built into the original EGA video hardware. As the IBM PC became very popular and
spread to other countries, more code pages were added to enable users to process and
store strings in their own languages as directly as possible. The terminology "code page"
originated with IBM's EBCIDIC-based mainframes. This is why the ASCII-based IBM PC
is code page 437, because earlier code page numbers were attached to EBCDIC variants.

In very short order, 8 bits was no longer sufficient to encode all the characters
required by a single user; languages like Japanese require thousands of characters for
even a minimal usable subset. The first serious effort was for Japanese, which introduced
multi-byte character sets, where the first character could be a "shift" character selecting
from a range of secondary tables, to allow some degree of compatibility with code that
knew how to process ASCII characters. DBCS (double byte character set) encodings followed
for Traditional Chinese, Korean, and Simplified Chinese.

## Code points

Starting with Unicode, the idea of the code point was solidified. That is, characters
map to abstract numbers known as code points. Then, code points can be encoded into bits
by using any one of a number of encoding schemes.

This didn't just make the process of encoding and decoding characters based on a character
set more straightforward, it also made it simpler to translate between character sets, because
you could use some universal code point system as the point of contact. Such a system doesn't
actually exist, although we can pretend that Unicode is that system, because Unicode was formed
originally by inspecting as many existing character sets as possible, as well as creating
new code points not satisfied by any existing character set.

## Reference

* [Character encoding](https://en.wikipedia.org/wiki/Character_encoding)

### Single-byte

* [ASCII](https://en.wikipedia.org/wiki/ASCII)
* [ISO/IEC 646](https://en.wikipedia.org/wiki/ISO/IEC_646)
* [Code page 437](https://en.wikipedia.org/wiki/Code_page_437)
* [Code page](https://en.wikipedia.org/wiki/Code_page)
* [EBCDIC](https://en.wikipedia.org/wiki/EBCDIC)
* [CCSID](https://en.wikipedia.org/wiki/CCSID). IBM's definition of code pages.
* [Shift JIS](https://en.wikipedia.org/wiki/Shift_JIS)

### Unicode

* [Unicode character table](http://unicode-table.com/en/). Useful browseable chart
of all Unicode characters.
* [UTF-8 Everywhere](http://utf8everywhere.org/). A manifesto declaiming the use of UTF-8 for
all manipulation of Unicode strings except as mandated by a specific API.

### Windows

Windows prefers UTF-16. All APIs come in two flavors: UNICODE, where the characters are
in UTF-16, and multibyte, where the characters are encoded according to a code page.

* [Reading and printing out Unicode and UTF-8 encodings in C](http://stackoverflow.com/questions/8028376/reading-and-printing-out-unicode-and-utf-8-encodings-in-c)
* [WideCharToMultiByte](https://msdn.microsoft.com/en-us/library/windows/desktop/dd374130(v=vs.85).aspx). Microsoft API function to convert UTF-16 strings to another encoding.
* [MultiByteToWideChar](https://msdn.microsoft.com/en-us/library/windows/desktop/dd319072(v=vs.85).aspx). Microsoft API to convert a multibyte encoding (including UTF-8) to UTF-16.
* [Console functions](https://msdn.microsoft.com/en-us/library/windows/desktop/ms682073(v=vs.85).aspx). Read/write directly to console.
* [Console class](https://msdn.microsoft.com/en-us/library/System.Console.aspx)
* [Changing font in a Console window in C#](http://stackoverflow.com/questions/20631634/changing-font-in-a-console-window-in-c-sharp)
* [Unicode at the Windows command prompt](http://illegalargumentexception.blogspot.com/2009/04/i18n-unicode-at-windows-command-prompt.html)

### Encoding/decoding

* [chardet 2.3.0](https://pypi.python.org/pypi/chardet). Detect a number of character encodings by inspecting the text.