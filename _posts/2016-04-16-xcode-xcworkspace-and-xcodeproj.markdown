---
layout: post
title:  "Xcode - xcworkspace and xcodeproj"
date:   2016-04-16 08:00:00 PM
categories: dev
tags: programming Xcode
---

Starting with Xcode 4 and through at least Xcode 6, the internals of the Xcode project have been
consistent. The only evolution has been to add new object types to the xcodeproj file.

There are two objects - the workspace (.xcworkspace) and the project (.xcodeproject). As of
Xcode 4, the workspace can now be a visible separate object.

# *.xcworkspace (external)

An Xcode workspace always exists, and may be external to an .xcodeproj, or embedded within one. We
will defer talking about embedded .xcworkspace directories for a bit.

Xcode workspaces are directories with the .xcworkspace extension
that the Mac OS X desktop presents as [packages](https://en.wikipedia.org/wiki/Package_(OS_X)).
In the package directory, the important file is contents.xcworkspacedata. A workspace is just a list of
contained projects; there is no other metadata.

The simplest possible .xcworkspace has an on-disk structure like this:

{% highlight bash %}
empty.xcworkspace/
└── contents.xcworkspacedata
{% endhighlight %}

and the `contents.xcworkspacedata` file, containing the actual workspace data, looks like this:

{% highlight xml %}
<?xml version="1.0" encoding="UTF-8"?>
<Workspace
    version = "1.0">
{% endhighlight %}

This workspace has nothing in it, but Xcode will open it up. Of course, once you open it up, Xcode
will likely generate other metadata like an `xcshareddata/` directory containing source control
information, and an `xcuserdata/` directory with user interface settings (window positions and
so on). That metadata isn't used for building, so we'll ignore it here.

A better example is from cppget, which has an Xcode workspace (albeit currently generated with
Premake and not hand-made). The current directory structure for this package is as follows:

{% highlight bash %}
cppget.xcworkspace/
├── contents.xcworkspacedata
├── xcshareddata/
│   └── cppget.xccheckout
└── xcuserdata/
    └── bfitz.xcuserdatad/
        ├── UserInterfaceState.xcuserstate
        ├── WorkspaceSettings.xcsettings
        └── xcdebugger/
            └── Breakpoints_v2.xcbkptlist
{% endhighlight %}

This is the contents of the workspace file, `cppget.xcworkspace/contents.xcworkspacedata`:

{% highlight xml %}
<?xml version="1.0" encoding="UTF-8"?>
<Workspace
    version = "1.0">
    <FileRef
        location = "group:cppget.xcodeproj">
    </FileRef>
    <FileRef
        location = "group:../vendor/__pkg__/cjson/project/cjson.xcodeproj">
    </FileRef>
    <FileRef
        location = "group:../vendor/__pkg__/popt/project/popt.xcodeproj">
    </FileRef>
    <FileRef
        location = "group:../vendor/__pkg__/zlib/project/zlib.xcodeproj">
    </FileRef>
    <FileRef
        location = "group:../vendor/__pkg__/minizip/project/minizip.xcodeproj">
    </FileRef>
    <FileRef
        location = "group:../vendor/__pkg__/curl/project/curl.xcodeproj">
    </FileRef>
    <FileRef
        location = "group:../vendor/__pkg__/unittest++/project/unittest++.xcodeproj">
    </FileRef>
</Workspace>
{% endhighlight %}

The document root object is `<Workspace>`. The root object has a `version` attribute
(always observed to be 1.0, indicating that the workspace file format hasn't been changing),
and then an array of child nodes of type `<FileRef>` and/or `<Group>`.
Each `FileRef` node has a single attribute, which is the location of the contained item.
The value of the attribute is a string with either a "self:" prefix or a "group:" prefix (the
self prefix is used when the .xcworkspace is embedded, see below). In the
case of the "group" prefix, this auto-creates a group containing just this item in the workspace.
Groups can contain Groups or FileRefs, but FileRefs are just leaf nodes.

In the example above, the source code structure looks like this:

{% highlight bash %}
cppget/
├── project/
│   └── cppget.xcworkspace/
└── vendor/
    └── __pkg__/
{% endhighlight %}

explaining the relative paths (getting to `vendor` from `project` requires `../vendor`).

I say "item" because you can put anything in an .xcworkspace. Normally, you put projects into
the workspace, but you can also put individual files, or directories. These will only be used for
browsing and search; to build, you need an .xcodeproj.

There is no idea of "main" project file. In this case, there is a main project file (cppget.xcodeproj)
that builds an executable, and then 6 additional projects that build libraries (each in the form of a
C/C++ package). When you open this workspace in Xcode, it in turn reads information from each project
referenced in the workspace file. All the data displayed in the GUI comes from each .xcodeproj,
the workspace itself contains no metadata.

There are other files in a typical .xcworkspace bundle. Usually, these are user-specific files that
hold settings. We'll defer covering those to some other time, as none of them alter the meaning
of the project files themselves.

## Adding a project to a workspace

Because a workspace just collects paths to projects, adding a project to a workspace is
very simple - just add a FileEntry node pointing to the project.

From C++, we would:

- read the XML with our preferred XML parser
- look to see if there is already a FileRef referring to our project
  - if yes, done
  - if not, add a new node

Note that we have no "update" ability; either we find the exact path to our new project already
in the workspace, or we add it. This means you need to take some care to have canonical paths.

The node we are adding follows the form

{% highlight xml %}
<FileRef location = "group:RELATIVEPATH"></FileRef>
{% endhighlight %}

e.g. the only data is the value for the `location` attribute, and the attribute value is prefixed
with either `group:` or `self:`.

So, for example, assuming you were using [RapidXML](http://rapidxml.sourceforge.net/),
your code might look like this. I have parsed the workspace into a data structure that has
pulled FileRefs out into a vector for easy manipulation, but then the XML itself has to be
manipulated to add a new FileRef.

{% highlight c++ %}
bool AddProjectToXcodeWorkspace(const std::string& projectPath)
{
    XCWorkspace workspace;
    if (!ReadXCWorkspace(config.var["xcode.workspace"], workspace))
        return false;
    
    if (std::find(workspace.project.begin(), workspace.project.end(), projectPath)
            != workspace.project.end())
        return true; // already in the workspace

    workspace.project.push_back(projectPath);
    
    // Insert into XML. Add to end of root node, which is Workspace
    auto& doc = workspace.doc;
    auto root = doc.first_node();
    auto fileref = doc.allocate_node(rapidxml::node_element, "FileRef");
    std::string groupPath = std::string("group:") + projectPath;
    char* attr_value = doc.allocate_string(groupPath.c_str());
    auto attr = doc.allocate_attribute("location", attr_value);
    fileref->append_attribute(attr);
    root->insert_node(nullptr, fileref);
    
    if (!WriteXCWorkspace(config.var["xcode.workspace"], workspace))
        return false;
    
    return true;
}
{% endhighlight %}

Perhaps a different XML library would have cleaner syntax. It's a tradeoff.

# *.xcodeproj

Like the workspace, an Xcode project is a bundle containing one or more files. The most important
and only required file is the `project.pbxproj` file.

The simplest possible project looks like this

{% highlight bash %}
empty.xcodeproj/
└── project.pbxproj
{% endhighlight %}

The `project.pbxproj` file is an Xcode-specific file format using the text plist format, which came
from NeXt and is now only used by legacy programs - and Xcode. Or so the internet claims.
Also interestingly, Xcode by default is reading and writing the ASCII
plist format, but can also read the XML plist format. But everyone seems to be creating the text
version, so we'll stick with that (because the XML plist format is pretty hard to read).

For a bigger example, let's look at the .xcodeproj from cppget:

{% highlight bash %}
cppget.xcodeproj/
├── project.pbxproj
└── xcuserdata/
    └── bfitz.xcuserdatad/
        └── xcschemes/
            ├── cppget.xcscheme
            └── xcschememanagement.plist
{% endhighlight %}

As with .xcworkspace directories, Xcode puts configuration data inside the project directory, which
we will ignore, because it doesn't bear directly on workspaces and projects at the build level.

The root of an Xcode project file is a dictionary. Looking at `cppget.xcodeproj/project.pbxproj`,
we see this:

{% highlight bash %}
// !$*UTF8*$!
{
    archiveVersion = 1;
    classes = {
    };
    objectVersion = 46;
    objects = {
    ... objects here
    };
    rootObject = 08FB7793FE84155DC02AAC07 /* Project object */;
}
{% endhighlight %}

`archiveVersion` has always been 1.  And `classes` is usually empty (always?).

`objectVersion` indicates the project format::

- 39: something really old, when the project files were called .xcode
- 42: Xcode 2.4
- 44: Xcode 3.0
- 45: Xcode 3.1 compatible
- 46: Xcode 3.2 compatible
- 47: Xcode 6.3 compatible

The most important key is `rootObject`, which tells us which object represents the project. The entire
project file is a tree, with `rootObject` at the top. The value of rootObject is an Xcode object
identifier that is an index into the `objects` dictionary, which is a PBXProject object.

## Xcode object identifiers

Objects have a 12-byte or 24-byte identifier, written as 24 or 48 hexadecimal characters; we'll call
them GUIDs here, although this does not imply similarity to other things people call GUIDs. Object
GUIDs need to be unique inside a project file, and also need to be unique among the set of project
files that are opened at the same time in Xcode. This means an effort should be taken to make them
unique across all projects. I don't know how true that needs to be, because most UUIDs are only used
locally to the project that they are found in.

There's an interesting competing need that Premake satisfies but the Xcode algorithm does not, and that
is that if you regenerate a project, it would be nice to have the same GUIDs, so that diffs are minimized
(e.g. when checking in to source control).

Premake uses a simple algorithm that is completely deterministic and relies on "paths" being unique.
Each element that gets a GUID has a sequence of strings that creates a virtual path, and this path is
hashed with a variant of the DJB algorithm. For example, creating a target ID uses the path
[projectname, configname, "target"]; this whole thing is turned into a string and hashed to get a
UUID. Since this is deterministic, regenerating the project will create the same UUID. The assumption
is that things that are named the same are the same thing. See uses of `xcode.newid` in
[premake/premake-xcode](https://github.com/premake/premake-xcode),
and refer to
[premake-core/src/host/string_hash.c](https://github.com/premake/premake-core/blob/master/src/host/string_hash.c)
for the low-level C code.

Xcode uses the algorithm linked here: [PBXProj Identifiers](https://pewpewthespells.com/blog/pbxproj_identifiers.html). This basically creates a memorized per-user structure that used with current time each time a
new identifer is created. This code was apparently reverse-engineered from DevToolsCore.framework.
This is safer, but requires some extra work if you want to minimize change on project regeneration.

## Xcode objects

All objects in the `objects` directory have an `isa` that indicates the type of object. As of Xcode 3.2,
there are the following object kinds

- PBXAppleScriptBuildPhase
- PBXBuildFile
- PBXContainerItemProxy
- PBXCopyFilesBuildPhase
- PBXFileReference
- PBXFrameworksBuildPhase
- PBXHeadersBuildPhase
- PBXGroup
- PBXNativeTarget
- PBXProject
- PBXReferenceProxy
- PBXResourcesBuildPhase
- PBXShellScriptBuildPhase
- PBXSourcesBuildPhase
- PBXTargetDependency
- PBXVariantGroup
- XCBuildConfiguration
- XCConfigurationList

## PBXProject

The root object points to an instance of PBXProject. Typically, there is just one in an .xcodeproj
file. The one for cppget looks like this:

{% highlight bash %}
08FB7793FE84155DC02AAC07 /* Project object */ = {
    isa = PBXProject;
    buildConfigurationList = 1DEB928908733DD80010E9CD;
    compatibilityVersion = "Xcode 3.2";
    hasScannedForEncodings = 1;
    mainGroup = ED4223A33B50CE509E91A9E3 /* cppget */;
    projectDirPath = "";
    projectReferences = (
        ... references here
    );
    projectRoot = "";
    targets = (
        9864FFB2FFF6A69F568155F2 /* cppget */,
    );
};
{% endhighlight %}

The `buildConfigurationList` key points to a XCConfigurationList object, which is an array
of XCBuildConfiguration objects. Many projects have a Debug and Release configuration, but
there are no mandatory configurations; projects can declare ones specific to their needs.
See below: there are two sets of config, one for projects, and the other for targets. This
is distinguished in the Xcode GUI but can be a subtle difference, since many settings are
shared.

The `mainGroup` key points to a PBXGroup object, which is the list of files that are built
for this project. As seen below, this is a list of individual items as well as other groups.
A group is just a decorative container, but Xcode will use standard names for some groups
like Products and Frameworks and Projects. Every file that contributes to the build will
be traced from `mainGroup`.

The `targets` key points to a list of targets that this project creates. These are:

- PBXNativeTarget: a build target that makes a binary (library or executable)
- PBXAggregateTarget: a build target that aggregates several others
- PBXLegacyTarget: (not supported any more?)

## PBXGroup

A PBXGroup object is a list of zero or more:

- PBXFileReference: a group can contain a reference to a file; this can be a source file, a framework, a build artifact (like a binary), or even another project.
- PBXReferenceProxy: a group can contain a reference to something from outside the project; this is usually an object built by a dependent project.
- PBXGroup: a group can contain another group, and this shows up as a sub-folder in the UI.

As an example:

{% highlight bash %}
EAEC10A24F22830F77FBC6E2 /* Products */ = {
    isa = PBXGroup;
    children = (
        ... references here
    );
    name = Products;
    sourceTree = "<group>";
};
{% endhighlight %}

This group has the name Products, has its source tree as the group itself, and then has zero or more children.
Typically groups are not empty.

## PBXFilereference

All physical items are identified by PBXFileReference objects.

A `sourceTree` entry is the location where this object can be found. Typically, this is `<group>`, meaning
that the location is inside the group containing this PBXFileReference (or the project itself). Sometimes
the `sourceTree` will be a variable like `BUILT_PRODUCTS_DIR`, which is either defined in the project itself
or externally (say as a default by Xcode).

The majority of entries will be source code, with a `lastKnownFileType` indicating the file type that Xcode
believes the file has. This is a hierarchy, so all source code tags start with `sourcecode.`; C++ files
begin with `sourcecode.cpp.`, and so on. So a C++ source file would be `sourcecode.cpp.cpp`, and a C
header file would be `sourcecode.c.h`.

{% highlight bash %}
0E6510F6A5C575A340791736 = {
    isa = PBXFileReference;
    lastKnownFileType = sourcecode.cpp.cpp;
    name = main.cpp;
    path = ../src/main.cpp;
    sourceTree = "<group>";
};
{% endhighlight %}

Some entries will indicate built objects, like an executable. These have an `explicitFileType` entry with a
value such as `compiled.mach-o.executable`.

{% highlight bash %}
5F42214CB908A5D9E0E93F8C = {
    isa = PBXFileReference;
    explicitFileType = "compiled.mach-o.executable";
    includeInIndex = 0;
    name = cppget;
    path = cppget;
    sourceTree = BUILT_PRODUCTS_DIR;
};
{% endhighlight %}

Some entries will indicate external items. These have a `lastKnowFileType` of `"wrapper.pb-project"`, and a `path`
pointing at the project itself, located relative to `sourceTree`. In this case a `sourceTree` of `SOURCE_ROOT`
indicates a variable set in Xcode pointing to the workspace or project.

{% highlight bash %}
2A74773383797DE0775EFD73 = {
    isa = PBXFileReference;
    lastKnownFileType = "wrapper.pb-project";
    name = "cjson.xcodeproj";
    path = ../packages/__pkg__/cjson/project/cjson.xcodeproj;
    sourceTree = SOURCE_ROOT;
};
{% endhighlight %}

Some entries indicate Mac OS X frameworks, either installed by the system (like LDAP.framework), or custom
to the project. In this case, `lastKnownFileType` is `wrapper.framework` and `path` points to the path for
the framework relative to `sourceTree`. In the example below, the source tree is `SDKROOT`, which is
predefined by Xcode to point to the system SDKs.

{% highlight bash %}
50F336C2F6C717AFAF740D02 = {
    isa = PBXFileReference;
    lastKnownFileType = wrapper.framework;
    name = LDAP.framework;
    path = System/Library/Frameworks/LDAP.framework;
    sourceTree = SDKROOT;
};
{% endhighlight %}

## PBXNativeTarget

A PBXNativeTarget objects describes how to build a native target. This is the various build phases,
the output name 

{% highlight bash %}
9864FFB2FFF6A69F568155F2 /* cppget */ = {
    isa = PBXNativeTarget;
    buildConfigurationList = 571A991BE942DF281F6C975B;
    buildPhases = (
        ... zero or more PBXBuildPhase
    );
    buildRules = (
    );
    dependencies = (
        ... zero or more PBXTargetDependency entries
    );
    name = cppget;
    productInstallPath = "$(HOME)/bin";
    productName = cppget;
    productReference = 5F42214CB908A5D9E0E93F8C /* cppget */;
    productType = "com.apple.product-type.tool";
};
{% endhighlight %}

In this example, this is an executable binary, as indicated by `productReference` pointing
to a PBXFileReference that is a `compiled.mach-o.executable`, but also by `productType`
being "command line tool", `com.apple.product-type.tool`.

{% highlight bash %}
5F42214CB908A5D9E0E93F8C = {
    isa = PBXFileReference;
    explicitFileType = "compiled.mach-o.executable";
    includeInIndex = 0;
    name = cppget;
    path = cppget;
    sourceTree = BUILT_PRODUCTS_DIR;
};
{% endhighlight %}

Note that a PBXNativeTarget has its own XCConfigurationList; these are named the same
as the project configurations, but contain target-specific information. See below for
the cascade order for config.

For example, the debug configuration has this as its target config:

{% highlight bash %}
41922E615426EFEE2E938CA1 /* Debug */ = {
    isa = XCBuildConfiguration;
    buildSettings = {
        PRODUCT_NAME = cppget;
    };
    name = Debug;
};
{% endhighlight %}

## PBXBuildPhase

There are at least these 7 kinds of build phases

- PBXAppleScriptBuildPhase: run an AppleScript
- PBXCopyFilesBuildPhase: copy files
- PBXFrameworksBuildPhase: link frameworks
- PBXHeadersBuildPhase: build precompiled headers
- PBXResourcesBuildPhase: build resources
- PBXShellScriptBuildPhase: run an sh shell script
- PBXSourcesBuildPhase: build sources

Of these, the most interesting to us is PBXSourcesBuildPhase.

## PBXSourcesBuildPhase

Without a PBXSourcesBuildPhase, an Xcode project is just an expensive container.

{% highlight bash %}
4273EAB3D49C30C00AC5E8F3 /* Sources */ = {
    isa = PBXSourcesBuildPhase;
    buildActionMask = 2147483647;
    files = (
        ... zero or more PBXBuildFile entries
    );
    runOnlyForDeploymentPostprocessing = 0;
};
{% endhighlight %}

This is just a list of PBXBuildFile entries, with a note as to whether this is done
in all builds or just for deployment post-processing (e.g. a PBXCopyFilesBuildPhase
would typically have this set).

## PBXBuildFile

This is the heart of building something in Xcode. There are several distinct kinds of
file builds.

First is source code. This just points to a PBXFileReference object that contains the
information actually needed to build it. The indirection is no doubt useful to Apple,
since the file entry is also used in a PBXGroup for display purposes.
For convenience, we show the PBXBuildFile entry and its associated PBXFileReference entry.

{% highlight bash %}
/* PBXBuildFile */
36E0BEAEF615B5FB417FCCEE = {isa = PBXBuildFile; fileRef = 0E6510F6A5C575A340791736; };

/* PBXFileReference */
0E6510F6A5C575A340791736 = {
    isa = PBXFileReference;
    lastKnownFileType = sourcecode.cpp.cpp;
    name = Slurp.cpp;
    path = ../src/Slurp.cpp;
    sourceTree = "<group>";
};
{% endhighlight %}

Not everything is a source code file. We also link static libraries built from elsewhere.
Note in this case that the reference is to a PBXReferenceProxy, indicating that this
file is from a dependent project, which is itself pointed to by a PBXContainerItemProxy,
which finally ends up at a PBXFileReference.

I don't know what `remoteGlobalIDString` is, I can't find that GUID in any project in
my hierarchy. Premake just assigns a value to it based on the hash of the path name (its
idea of the hierarchy to this point).

{% highlight bash %}
/* PBXBuildFile */
FADCF8DBA93E5EC82C820F1B = {isa = PBXBuildFile; fileRef = 131E5623D4E73570AF0F6463; };

/* PBXReferenceProxy */
131E5623D4E73570AF0F6463 = {
    isa = PBXReferenceProxy;
    fileType = archive.ar;
    path = libzlib.a;
    remoteRef = 89541513EE81084016813B53 /* PBXContainerItemProxy */;
    sourceTree = BUILT_PRODUCTS_DIR;
};

/* PBXContainerItemProxy */
89541513EE81084016813B53 = {
    isa = PBXContainerItemProxy;
    containerPortal = C48570AFF3172E7CF2D39EEF;
    proxyType = 2;
    remoteGlobalIDString = 0E765FC0A32BB52D9353D600;
    remoteInfo = libzlib.a;
};

/* PBXFileReference */
C48570AFF3172E7CF2D39EEF = {
    isa = PBXFileReference;
    lastKnownFileType = "wrapper.pb-project";
    name = "zlib.xcodeproj";
    path = ../packages/__pkg__/zlib/project/zlib.xcodeproj;
    sourceTree = SOURCE_ROOT;
};
{% endhighlight %}

Frameworks are semi-magic libraries with versioning and header files used with the
libraries. For the build phase, this is linking a framework, so it just ends up directly
at the PBXFileReference, no indirection needed.

{% highlight bash %}
/* PBXBuildFile */
F8E8DBA2B7DE2E6FF44F49E2 = {isa = PBXBuildFile; fileRef = 8D6BC6AAF7BB96D78F7B2CEA; };

/* PBXFileReference */
8D6BC6AAF7BB96D78F7B2CEA = {
    isa = PBXFileReference;
    lastKnownFileType = wrapper.framework;
    name = Cocoa.framework;
    path = System/Library/Frameworks/Cocoa.framework;
    sourceTree = SDKROOT;
};
{% endhighlight %}

## XCConfigurationList

An XCConfigurationList object is just a list of XCBuildConfiguration objects.

{% highlight bash %}
1DEB928908733DD80010E9CD /* Build configuration list for PBXProject "cppget" */ = {
    isa = XCConfigurationList;
    buildConfigurations = (
        B3315B92708358DFD7FBE9D2 /* Debug */,
        395E624C9324E6D9BB05808C /* Release */,
    );
    defaultConfigurationIsVisible = 0;
    defaultConfigurationName = Debug;
};
{% endhighlight %}

The `defaultConfigurationName` key indicates which configuration, by name, is the one to
pick as the default when a project is opened for the first time.

The `defaultConfigurationIsVisible` key, if set to non-zero, shows the default configuration
information in the UI.

## XCBuildConfiguration

An XCBuildConfiguration object contains the settings that describe the configuration. At its
minimum, a build configuration looks like this:

{% highlight bash %}
        B3315B92708358DFD7FBE9D2 /* Debug */ = {
            isa = XCBuildConfiguration;
            buildSettings = {
                ONLY_ACTIVE_ARCH = YES;
            };
            name = Debug;
        };
{% endhighlight %}

This is an example where a name needs to be unique, because, for example, the UI will look up
a configuration by name and not by UUID. Although, it must be doing some disambiguation,
because in a typical project there are two distinct XCConfigurationList objects, one for
the project object, and one for the PBXNativeTarget object.

Any settings not specified here come from Xcode defaults. There is a cascade for configuration.
Inheritance is performed in the following order (lowest to highest precedence):

- platform defaults
- project
- xcconfig file for project
- target
- xcconfig file for target

Value assignment is performed in the following order (lowest to highest precedence):

- platform defaults
- xcconfig for project file
- project
- xcconfig for target
- target

The distinction is due to handling of inheritance. TBD: explain.

# *.xcworkspace (internal)

It is possible to just have an .xcodeproj visible to the desktop, and have a .xcworkspace directory
inside the .xcodeproj directory. In this case, the workspace is always called `project.xcworkspace`
and is at the root directory of the project. This is what you get if you use the Xcode wizard to
create a new project first, instead of creating a workspace and adding projects to it.

The simplest possible arrangement is to have the following:

{% highlight bash %}
one.xcodeproj/
├── project.pbxproj
└── project.xcworkspace/
    └── contents.xcworkspacedata
{% endhighlight %}

where `project.xcworkspace/contents.xcworkspacedata` look like this:

{% highlight xml %}
<?xml version="1.0" encoding="UTF-8"?>
<Workspace
   version = "1.0">
   <FileRef
      location = "self:one.xcodeproj">
   </FileRef>
</Workspace>
{% endhighlight %}

The `self` tag points to the top level of the `one.xcodeproj` directory. The `project.pbxproj` is
as detailed above.

# To-do

Write a DTD for `contents.xcworkspacedata` files. And maybe a grammar; are DTDs comprehensive
enough that they can serve as prescriptive grammars? Looks like not really. Or maybe an XML Schema?

Compile a list of more Xcode versions matched up againts `objectVersion`. What was before objectVersion 45?

A few Xcode projects to test a xcworkspace parser against:

- [https://github.com/mattt/MsgPackSerialization/blob/master/MsgPackSerialization.xcworkspace/contents.xcworkspacedata](https://github.com/mattt/MsgPackSerialization/blob/master/MsgPackSerialization.xcworkspace/contents.xcworkspacedata)
- [https://github.com/MSOpenTech/orc-for-ios/blob/master/orc.xcworkspace/contents.xcworkspacedata](https://github.com/MSOpenTech/orc-for-ios/blob/master/orc.xcworkspace/contents.xcworkspacedata)
- [https://github.com/seatgeek/SGAPI/blob/master/SGAPI.xcworkspace/contents.xcworkspacedata](https://github.com/seatgeek/SGAPI/blob/master/SGAPI.xcworkspace/contents.xcworkspacedata)
- [http://opensource.apple.com//source/libutil/libutil-30/libutil.xcodeproj/project.xcworkspace/contents.xcworkspacedata](http://opensource.apple.com//source/libutil/libutil-30/libutil.xcodeproj/project.xcworkspace/contents.xcworkspacedata)
- [https://github.com/StefanLage/Location-Generator/blob/master/Location%20Generator.xcodeproj/project.xcworkspace/contents.xcworkspacedata](https://github.com/StefanLage/Location-Generator/blob/master/Location%20Generator.xcodeproj/project.xcworkspace/contents.xcworkspacedata)

A few Xcode projects to test a xcodeproj parser against:

- [https://github.com/jacobsimeon/Pygments/blob/master/Pygment.xcodeproj/project.pbxproj](https://github.com/jacobsimeon/Pygments/blob/master/Pygment.xcodeproj/project.pbxproj)

# Reference

There is no formal documentation. Others have done their share of reverse engineering the project format,
for their needs. Premake and CMake generate project files, CocoaPods is a package manager for Mac OS/iOS
developers, and so on. Here is a collection of what other people have written.

[A dive into Xcode projects and workspaces](http://www.mokacoding.com/blog/xcode-projects-and-workspaces/).

[Xcode Project File Format](http://www.monobjc.net/xcode-project-file-format.html).

A series of articles on the Xcode project format:

- [A brief look at the Xcode project format](http://danwright.info/blog/2010/10/xcode-pbxproject-files/)
- [More on the Xcode project format](http://danwright.info/blog/2010/10/xcode-pbxproject-files-2/)
- [Xcode project object UUIDs](http://danwright.info/blog/2010/10/xcode-pbxproject-files-3/)
- [Xcode build configurations](http://danwright.info/blog/2012/10/)
- [Xcode project files (bsxtools)](http://danwright.info/blog/2013/01/) - points to Github repo [danwr/bsxtools](https://github.com/danwr/bsxtools)

[OpenOffice.org XML File Format 1.0](https://www.openoffice.org/xml/xml_specification.pdf). An example of a complex XML file format.

A number of projects that can manipulate Xcode projects

- [XCoder](https://github.com/rayh/xcoder). A no-longer-maintained Ruby project to manipulate Xcode
projects. Lots of comments in the source, though.
- [Xcodeproj](http://www.rubydoc.info/gems/xcodeproj). This is the library used by CocoaPods to
create static libraries from scratch for iOS and OSX. Github is [https://github.com/CocoaPods/Xcodeproj](https://github.com/CocoaPods/Xcodeproj)
- [node-xcode](https://github.com/alunny/node-xcode). Includes a PEG grammar.
- [kronenthaler/mod-pbxproj](https://github.com/kronenthaler/mod-pbxproj). Python.
- [appsquickly/XcodeEditor](https://github.com/appsquickly/XcodeEditor).

[CocoaPods](https://cocoapods.org/). This is a package manager for Swift and Objective-C projects. Source
code is on Github at [CocoaPods/CocoaPods](https://github.com/CocoaPods/CocoaPods).

[Old-Style ASCII Property Lists](https://developer.apple.com/library/mac/documentation/Cocoa/Conceptual/PropertyLists/OldStylePlists/OldStylePLists.html). Except this doesn't seem to match observed project.pbxproj
files.

[Xcode Build Settings Reference](https://pewpewthespells.com/blog/buildsettings.html).

[The Unofficial Guide to xcconfig files](https://pewpewthespells.com/blog/xcconfig_guide.html)

[Xcode Workspace](https://developer.apple.com/library/ios/featuredarticles/XcodeConcepts/Concept-Workspace.html)

[Xcode Build System Guide (legacy)](https://developer.apple.com/legacy/library/documentation/DeveloperTools/Conceptual/XcodeBuildSystem/Xcode_Build_System.pdf)
