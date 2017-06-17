---
categories: dev
date: 2016-03-17T22:14:00Z
tags: programming VisualStudio
title: Visual Studio notes
url: /2016/03/17/visual-studio-notes/
---

For those of us who want to work on Visual Studio project files:

[A guide to .vcxproj and .props file structure](https://blogs.msdn.microsoft.com/visualstudio/2010/05/14/a-guide-to-vcxproj-and-props-file-structure/). This is a 6-year old post, but the solution and project file formats haven't changed substantially since then.

[Hand-constructing Visual Studio 2012 vcxproj](http://blog.bfitz.us/?p=922). An older post where I talked about minimum required elements in a vcxproj.

[VISUAL STUDIO 2012 SOLUTION FILE GRAMMAR](http://blog.bfitz.us/?p=955). A stab at a Visual Studio solution grammar.

[Build System Changes](https://msdn.microsoft.com/en-us/library/ee862524.aspx)

[MSBuild (Visual C++)](https://msdn.microsoft.com/en-us/library/dd293626.aspx). Since MSBuild is used to drive builds via .vcxproj files, knowing MSBuild can help you understand the items in a .vcxproj file.

[MSBuild Project File Schema Reference](https://msdn.microsoft.com/en-us/library/5dy88c2e.aspx)

[Project Files](https://msdn.microsoft.com/en-us/library/2208a1f2.aspx)

[Working with both VS 2010 and 2008 on the team](http://robrich.org/archive/2010/04/20/working-with-both-vs-2010-and-2008-on-the-team.aspx)

[Sharing project properties in Visual C++](http://manski.net/2012/01/sharing-project-properties-in-visual-c/)

[Getting Visual Studio version of a Solution file](http://pascoal.net/2011/05/getting-visual-studio-version-of-a-solution-file/)

[Question about Visual Studio *.sln file format](http://stackoverflow.com/questions/5629981/question-about-visual-studio-sln-file-format). An explanation for why the files are so messed up.

[Solution (.Sln) File](https://msdn.microsoft.com/en-us/library/bb165951.aspx)

[What does the “Link Library Dependency” linker option actually do in Visual Studio 2010?](http://stackoverflow.com/questions/7683869/what-does-the-link-library-dependency-linker-option-actually-do-in-visual-stud)

[Premake5 alpha6 issue - ProjectReference's in vcxproj have disappeared](https://groups.google.com/forum/#!topic/premake-development/x-cCFNBKVC0)

[dependson can break links in VS](https://github.com/premake/premake-core/issues/208)

```
MSVC++ 14.0 _MSC_VER == 1900 (Visual Studio 2015)
MSVC++ 12.0 _MSC_VER == 1800 (Visual Studio 2013)
MSVC++ 11.0 _MSC_VER == 1700 (Visual Studio 2012)
MSVC++ 10.0 _MSC_VER == 1600 (Visual Studio 2010)
MSVC++ 9.0  _MSC_VER == 1500 (Visual Studio 2008)
MSVC++ 8.0  _MSC_VER == 1400 (Visual Studio 2005)
MSVC++ 7.1  _MSC_VER == 1310 (Visual Studio 2003)
MSVC++ 7.0  _MSC_VER == 1300
MSVC++ 6.0  _MSC_VER == 1200
MSVC++ 5.0  _MSC_VER == 1100
```
