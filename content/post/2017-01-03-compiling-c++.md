---
categories: dev
date: 2017-01-03T11:27:00Z
tags: C++
title: Compiling C++
url: /2017/01/03/compiling-c++/
---

This is a continuation of my last post.

The C++ standard lists 9 phases of translation. Some are trivial, some are massive (phase 7 is "compile
after preprocessing").

1. Map physical source file characters to the basic source character set
2. Splice lines together as needed
3. Break text into sequences of preprocessing tokens
4. Evaluate preprocessing directives and expand macros
5. Convert character and string literals from source character set to execution character set
6. Concatenate adjacent string literals
7. Compile: convert preprocessing tokens to tokens and compile into translation units
8. Combine translation units and instantiation units (for templates)
9. Resolve external references and produce final program image

# Phase 1 - map to basic source character set

This can be a no-op in compilers if the physical source file characters are identical to the basic
source character set. In modern compilers, there may be some text to text translation, especially
for handling newline characters.

Without loss of generality, we could state that the basic source character set is UTF-8 and that
newline characters are linefeeds, e.g. 0x0A. We would translate our input file to match this.

As a reminder, this is the basic character set:

    a b c d e f g h i j k l m n o p q r s t u v w x y z
    A B C D E F G H I J K L M N O P Q R S T U V W X Y Z
    0 1 2 3 4 5 6 7 8 9
    _ { } [ ] # ( ) < > % : ; . ? * + - / ^ & | ~ ! = , \ " ’

It is augmented with universal-character-name characters for any character outside this range.
In theory, this means the use of escapes like `\u00F6` instead of direct Unicode characters like
`ö`. However, the "as-if" rule applies here: the standard states that "an implementation may use
any internal encoding, so long as..." it works out the same.

This also includes conversion of trigraphs, if trigraphs are supported. Trigraph support is not required
as of C++14 and is slated to be removed in C++17. No one should be using trigraphs.

Raw string literals: Trigraph and universal character translations would be reversed in phase 3. It is
ignored here because identifying raw string literals is done in the preprocessor tokenization phase.

# Phase 2 - splice lines together

The C++ standard says that lines ending in backlash `\` are joined together with the following line
to create a single logical line. The main reason for this is for long string literals and for long
preprocessor directives. The preprocessor is line-based in that a preprocessor directive must be on
a single logical line. By splicing lines together before interpreting directives, we can make macros
that are more readable than they would be otherwise.

# Phase 3 - decompose file into preprocessor tokens

This phase is an "as-if" phase. A compiler could elect to work with the text itself instead of the
preprocessor tokens. However, there are some subtleties that make it a lot easier to talk about the
process as if there were tokens.

Raw string literals are an interesting exception. If the start of a raw-string literal is detected,
then the universal-character-name translation in phase 1 and the line-splicing in phase 2 are reverted,
and the raw string is the shortest sequence of characters that match the raw string pattern. This
means for example that a raw string defined as such:

    char* s = R"(my\
    string)";

would be the literal characters `my\<newline>string`.

Now, does this mean that it's the newline from the physical file? While the standard reads as ambiguous,
I'm pretty sure this has to be a newline character - if the execution character set is UTF-8, then
the string would be:

    74 68 69 73 5c 0a 73 74 72 69 6e 67 00

regardless of the type of newline in the physical file.

Another exception is the `<:` digraph; if the sequence `<::` is followed by anything other than `:` or `>`, then
this is treated as the token `<` and not the digraph `<:`. This is a little hard to puzzle out. The string `<::>`
would be digraphs for `[]`, so that part makes sense, e.g a declaration such as `int main(int argc, char* argv[])`.
But it's a little harder to see what `<:::` would mean.

Personally, I think compilers should refuse to implement the punctuation diagraphs, and it look like MSVC
does not support them (and maybe never did). I'm neutral on the keyword "diagraphs" like `and`, `or` etc.
I feel like those are too much like trying to pretend C++ is a different language. I also notice that
MSVC doesn't directly support them, only through a header file <iso646.h> where it creates macros for them...
so not really digraphs. Just say no to digraphs.
