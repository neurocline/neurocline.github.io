---
categories: dev
date: 2015-11-01T23:05:00Z
tags: compiler parser
title: Generalized Deterministic Parsers (GLR, GLL and more)
url: /2015/11/01/generalized-deterministic-parsers/
---

Generalized deterministic parsing is an extension to deterministic parsing that
temporarily handles ambiguous grammars under the idea that further input will resolve
apparent conflicts.
The first such one was called Generalized LR or GLR, under the idea that this technique
(forking and parsing parallel parse trees each time an ambiguity was found) would only
work with LR. Then, once a GLL parser was demonstrated, it was hard to call the
method GLR. I'm going by the naming that Grune and Jacobs gave it in their 2007
masterpiece Parsing techniques.

Bison supports GLR, and I don't know why it's not the default. GLR only costs more than
LR when you have ambiguities in the grammar, and even C++ has only temporary ambiguities
that resolve quickly. Also, you don't need to try to rewrite the grammar to create a
LALR parser.

[A Practical GLR Parser Generator for Software Reverse Engineering](http://www.researchgate.net/publication/269645727_A_Practical_GLR_Parser_Generator_for_Software_Reverse_Engineering) is a paper behind a gate in ResearchGate, e.g. [here](http://ojs.academypublisher.com/index.php/jnw/article/download/jnw0903769776/8857)

[DParser](http://dparser.sourceforge.net/), although doesn't seem to have had much activity since initial creation in 2006.

[Integrating a GLR Parser Generator in Eli](http://www.researchgate.net/publication/241755950_Integrating_a_GLR_Parser_Generator_in_Eli) 2007 thesis. Direct PDF link [here](http://ag-kastens.uni-paderborn.de/paper/Bachelor_Schwekendiek.pdf)

[Island Grammar-Based Parsing Using GLL and Tom](https://pure.royalholloway.ac.uk/portal/en/publications/island-grammarbased-parsing-using-gll-and-tom(1bccc756-7ebc-4b14-abd2-8910ef5dcbeb).html). I think Adrian Johnstone is the one who demonstrated the possibility of GLL parsers.

[GLL parse-tree generation](https://pure.royalholloway.ac.uk/portal/en/publications/gll-parsetree-generation(bdbcf5af-d176-4389-bfcc-ecc86a5a5f77).html)

[Modular grammar specification](https://pure.royalholloway.ac.uk/portal/en/publications/modular-grammar-specification(f73cedc9-678f-4115-8802-2add7aa0763d).html)

[Modelling GLL parser implementations](https://pure.royalholloway.ac.uk/portal/en/publications/modelling-gll-parser-implementations(2c1e93df-746c-4271-8de6-534141d0c9b5).html)
