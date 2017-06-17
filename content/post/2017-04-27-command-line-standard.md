---
categories: dev
date: 2017-04-27T03:06:00Z
tags: command-line
title: Command-line standard
url: /2017/04/27/command-line-standard/
---

I need a good command-line parsing library that I can use in C++ programs. There aren't any - good
ones, that is. What is a good command-line parser? It's easy to integrate, easy to use, and supports
both trivial use cases (a handful of options) and huge use cases (Git, with its hundreds of sub-commands).

More importantly, there is a lot of variation in how people make command-lines, and a lot of inconsistency.
What would a standard look like?

Command lines exist even for non-command-line environments. Services can have command lines defined
for them, GUIs might have command-lines passed to them at launch, and so on. There is some unspecified
tokenization happening that breaks up a string of characters into a list of values. These values are
then interpreted as zero or more command-line arguments, which have meaning to the specific program
that is being executed.

# Positional parameters

Positional parameters are referenced by their position on the command-line. It is typical but
not required that positional parameters are mandatory.

## No parameters

The most trivial command-line invocation looks like this:

```
$ pwd
/Users/bfitz/projects
```

This command has no parameters. It's still a command-line, and there are useful command-line
programs with no parameters. This is very easy to code towards - do nothing with the command-line.

```
int main(int, char**)
{
	do_work();
}
```

## One parameter

Going one step up in complexity, we have command-lines with a single mandatory parameter.

```
$ cd z
```

This command has a single parameter, which for the `cd` command is the new desired working
directory. This is easy to handle - just grab the first and single item on the command line
(remembering that the 0th entry in `argv` is the name of the program itself):

```
int main(int, char* argv[])
{
	do_work(argv[1]);
}
```

## Multiple parameters

Generalizing this past one, we talk about the general case as having positional parameters,
because each parameter is in a specific position: first, second, third etc. In fact, the
POSIX standard passes the commandline as individual arguments to your C `main()` function via
an array of positional parameters.

For the `cp` (copy-file) function, we copy the file referenced by first positional parameter to the location
specified by the second positional parameter:

```
$ cp results.txt results-backup.txt
```

which would look like this

```
int main(int, char* argv[])
{
    char* srcfile = argv[1];
    char* dstfile = argv[2];
	copy_file(srcfile, dstfile);
}
```

## Naming positional parameters

Conceptually, we can assign names to our positional parameters. We did this in our same program above,
but it's nice to let the user know what the positional parameters mean, in whatever documentation we
provide. One of the most useful forms of documentation is command-line help. Command-line help for
the simplified cp command might look like this:

```
usage: cp source_file target_file

copy the contents of source_file to target_file
```

We call them positional parameters because the user must supply them in the order that the program
is expecting. These don't need to be mandatory, but if we do provide them, we have to start from the
first parameter and go to the last parameter we supply.

Unfortunately for us, some commands allow for overloaded command lines, and the `cp` command
is a good example. It has two forms, the one shown above, and another that copies a list of files
to a target directory:

```
$ cp first.txt second.txt backup
```

This would copy the two files `first.txt` and `second.txt` to similarly-named files in the
`backup` directory. In this case, the second parameter does not have the logical name target_file.
Instead, our definition looks like this

```
usage: cp source_file ... target_directory
```

where we have two or more source files that we copy into a target directory. If we think about
this as a grammar, we might have

```
cp ::= 'cp' singlefile|multifile
singlefile ::= source_file target_file
multifile ::= sourcelist target_directory
sourcelist ::= source_file source_file+
source_file ::= string
target_file ::= string
target_directory ::= string
```

Of course, this is awkward for people to read, even if it is precise enough for a program to use.
And we still have multiple parameters named source_file. I don't have a way to label this yet.

# Named parameters

Named parameters are referenced by their name, not by their position. For the most part,
named parameters can be presented in any order, although there are some command line patterns
that have a semi-ordering of parameters. It is typical that named parameters are optional,
but it is possible that some named parameters are mandatory.

It is very common for these to be called options, because the first use of named parameters
was to add options to commands that contained positional parameters.

Almost universally, named parameters begin with a `-` character, and in fact we typically
refer to the named parameter by eliding the beginning `-` or `--` prefix.

Named parameters might have no argument, one argument, or multiple arguments. When a named
parameter has no arguments, it is typically treated as a boolean, where if present, the
boolean is true, and if not present, the boolean is false.

The GNU system introduced a convention for short versus long names. Originally, "options"
were single-character names that could be bundled together, e.g the following two lines are
equivalent.

```
tar -x -v -c -f backup.tar
tar -xvcf backup.tar
```

As command-lines became more complex, single-character options fell into disrepute; most named
parameters are multi-character. The GNU extension was to mandate the use of `--` as a prefix,
to prevent confusion with bundled single-character options.

### Named parameters with no arguments

Consider the following:

```
$ git --version
git version 2.12.2
```

The git command is complex, but among its many options is `--version`, which prints the git
version. It has no arguments, and in this form, needs no parameters.
