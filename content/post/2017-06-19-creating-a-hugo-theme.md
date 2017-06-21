+++
Categories = []
Description = ""
Tags = []
date = "2017-06-19T12:50:38-07:00"
title = "Creating a Hugo theme"

+++

So, I've embraced Hugo for now; I'll write my own bespoke system some other day.

I want a specific look, so I need to create my own Hugo theme.

My layout is fairly typical for a Hugo site:

```
~/projects/github/neurocline.github.io
├── config.yaml
├── content/
│   ├── draft/
│   ├── papers/
│   └── posts/
├── public/
├── static/
│   ├── assets/
│   └── css/
└── themes/
    └── herring-cove/
        ├── archetypes/
        ├── images/
        ├── layouts/
        ├── static/
        └── theme.toml
```

My `config.yaml` (the fact that it's YAML is a legacy of the conversion from Jekyll that I haven't
changed yet) points to the theme so that I don't need to pass it on the command-line when generating:

```yaml
baseURL: http://neurocline.github.io
disablePathToLower: true
languageCode: en-us
title: Some Things Are Obvious
SectionPagesMenu: main
theme: herring-cove
```

## New theme

So first, let's create a new theme:

```
$ hugo new theme hugo-neurocline
```

and this creates a new directory in `themes`:

```
~/projects/github/neurocline.github.io
...
└── themes/
    ├── herring-cove/
    └── hugo-neurocline/
        ├── archetypes/
        ├── layouts/
        ├── LICENSE.md
        ├── static/
        └── theme.toml
```

This is a tiny bit of boilerplate and some directories to populate with files. I'll keep the MIT license
and update the `theme.toml` with relevant info.

Using the theme at this point is easy but counter-productive. Still, try it. Edit the top-level `config.yaml`
to point to the new theme:

```
theme: hugo-neurocline
```

And now the blog contents are gone. Well, that's not precisely true. An index.xml file is generated that
will be rendered by the browser, but there is no index.html or any other html pages. Here's the output

```
$ hugo -D -v --cleanDestinationDir serve
INFO 2017/06/20 13:29:59 Using config file:
INFO 2017/06/20 13:29:59 using a UnionFS for static directory comprised of:
INFO 2017/06/20 13:29:59 Base: C:\projects\github\neurocline\neurocline.github.io\themes\hugo-neurocline\static
INFO 2017/06/20 13:29:59 Overlay: C:\projects\github\neurocline\neurocline.github.io\static\
INFO 2017/06/20 13:29:59 removing all files from destination that don't exist in static dirs
INFO 2017/06/20 13:29:59 syncing static files to C:\projects\github\neurocline\neurocline.github.io\
WARN 2017/06/20 13:29:59 No translation bundle found for default language "en"
WARN 2017/06/20 13:29:59 Translation func for language en not found, use default.
WARN 2017/06/20 13:29:59 i18n not initialized, check that you have language file (in i18n) that matches the site language or the default language.
Started building sites ...
INFO 2017/06/20 13:29:59 found taxonomies: map[string]string{"tag":"tags", "category":"categories"}
WARN 2017/06/20 13:29:59 [en] Unable to locate layout for "page": [papers/post.html.html papers/post.html _default/post.html.html _default/post.html theme/papers/post.html.html theme/papers/post.html theme/_default/post.html.html theme/_default/post.html]
WARN 2017/06/20 13:29:59 [en] Unable to locate layout for "taxonomyTerm": [taxonomy/category.terms.html.html taxonomy/category.terms.html _default/terms.html.html _default/terms.html indexes/indexes.html.html indexes/indexes.html theme/taxonomy/category.terms.html.html theme/taxonomy/category.terms.html theme/_default/terms.html.html theme/_default/terms.html theme/indexes/indexes.html.html theme/indexes/indexes.html]
WARN 2017/06/20 13:29:59 [en] Unable to locate layout for "taxonomyTerm": [taxonomy/tag.terms.html.html taxonomy/tag.terms.html _default/terms.html.html _default/terms.html indexes/indexes.html.html indexes/indexes.html theme/taxonomy/tag.terms.html.html theme/taxonomy/tag.terms.html theme/_default/terms.html.html theme/_default/terms.html theme/indexes/indexes.html.html theme/indexes/indexes.html]
Built site for language en:
3 of 3 drafts rendered
0 future content
0 expired content
221 regular pages created
182 other pages created
0 non-page files copied
0 paginator pages created
7 categories created
78 tags created
total in 58 ms
WARN 2017/06/20 13:29:59 Skip dataDir: GetFileAttributesEx C:\projects\github\neurocline\neurocline.github.io\data: The system cannot find the file specified.
WARN 2017/06/20 13:29:59 Skip i18nDir: GetFileAttributesEx C:\projects\github\neurocline\neurocline.github.io\i18n: The system cannot find the file specified.
WARN 2017/06/20 13:29:59 Skip layoutDir: GetFileAttributesEx C:\projects\github\neurocline\neurocline.github.io\layouts: The system cannot find the file specified.
Watching for changes in C:\projects\github\neurocline\neurocline.github.io\{content,static,themes}
WARN 2017/06/20 13:29:59 Skip dataDir: GetFileAttributesEx C:\projects\github\neurocline\neurocline.github.io\data: The system cannot find the file specified.
WARN 2017/06/20 13:29:59 Skip i18nDir: GetFileAttributesEx C:\projects\github\neurocline\neurocline.github.io\i18n: The system cannot find the file specified.
WARN 2017/06/20 13:29:59 Skip layoutDir: GetFileAttributesEx C:\projects\github\neurocline\neurocline.github.io\layouts: The system cannot find the file specified.
Serving pages from memory
Web Server is available at http://localhost:1313/ (bind address 127.0.0.1)
```

I am a little mystified that things are showing up that I don't expect. This is my `theme.toml`

```toml
# theme.toml template for neurocline.github.io
# See https://github.com/gohugoio/hugoThemes#themetoml for an example

name = "Hugo Neurocline"
license = "MIT"
licenselink = "https://github.com/neurocline.github.io/hugo-neurocline/blob/master/LICENSE.md"
description = "Theme for the Some Things Are Obvious blog"
homepage = "http://neurocline.github.io/"
tags = []
features = []
min_version = "0.23"

[author]
  name = "Brian Fitzgerald"
  homepage = "http://neurocline.github.io/"
```

and yet when I generate, I see it declaring it found taxonomies for "tags" and "categories":

```
INFO 2017/06/20 13:29:59 found taxonomies: map[string]string{"tag":"tags", "category":"categories"}
```

I think this is because I didn't declare any taxonomies in the top-level `config.yaml`, and the
Hugo docs do say this: "The default taxonomies for Hugo are _tags_ and _categories_." I'll get
to taxonomies in a bit.

## index.html - the home page

The root of your site as seen by a user has a page that by long-standing convention is called
`index.html`. This is what people think of as your site, and is often called the landing page.

The `hugo new theme` command created a template, but it's empty. Let's create something
bit by bit, using Hugo's shortcodes, which default to being the Go template engine.

First, the minimal HTML

```html
<!DOCTYPE html>
<html>
<body>
</body>
</html>
```

Then, inside the `body` section, we can output a list of all the files in `content`:

```html
<body>
  <h1>Pages</h1>
  {{ range .Data.Pages }}
    <p>{{ .Title }}</p>
  {{ end }}
</body>
```

The `range` iterator defaults to reverse chronological order, e.g. newest first. This works
by the `date` attribute in each page, not the filesystem modtime (which could be fairly
arbitrary, given how source control systems tend to work). If you have multiple sections
in `content`, this line will mix them all together.

Hugo template variable instantiation is context-sensitive. The line `{{ range .Data.Pages }}`
is running at the top-level of your site, but inside that loop, the line `{{ .Title }}`
has the context of the page being iterated. This makes for terse templates, just a little
hard to make sense of at first. Or, rather, it tends to match your intuitions, but makes it
just a little harder to reason about when you are getting started.

The variable `.Data` is populated with every content file at the start of operation.

## single.html - a content page

Our pages themselves need to be rendered. The simplest way to do this is with the
theme `_default/single.html` template.

Start with the minimal HTML:

```html
<!DOCTYPE html>
<html>
<head>
</head>
<body>
</body>
</html>
```

We need a title, so we update `head`:

```html
<head>
  <title>{{ .Title }}</title>
</head>
```

This makes the HTML title attribute be the Title attribute from our page. This shows up
as the name of the tab or page in the browser.

We also need a body, so we update `body`:

```html
<body>
  <h1>{{ .Title }}</h1>
  {{ .Content }}
</body>
```

This makes the page start with an `h1` header of our Title, and then follows with the
translated content of the body.

Of course, we can't see this HTML without reading the files themselves, so we want to link
from our pages via the home page text. So change `index.html`:

```html
<!DOCTYPE html>
<html>
<body>
  <h1>Pages</h1>
  {{ range .Data.Pages }}
    <p><a href="{{ .Permalink }}">{{ .Title }}</a></p>
  {{ end }}
</body>
</html>
```

And now, we have a circa-1990s web site! Functional and ugly!

## Fixing URLS

Hugo defaults to what it calls "pretty URLS" - e.g. a file `content/post/2017-06-19-creating-a-hugo-theme.md`
will be rendered to `public/post/2017-06-19-creating-a-hugo-theme/index.html`. This supports the
idea of a browser link like `http://neurocline.github.io/post/2017-06-19-creating-a-hugo-theme/`, since
users think of URLS by a name lacking a suffix like `.html`.

On the one hand, I balk at having many hundreds of directories on a web server. On the other hand,
who cares?

To have URLS resolve to files, do two things.

First, tell Hugo to generate "ugly URLS" by adding an item to the site-wide `config.toml`.

```toml
uglyurls = true
```

This will cause generated files to have the "ugly" syntax, and it will also make sure that variables
like `{{ .Permalink }}` use the ugly syntax.

My old Jekyll site used the "ugly" syntax, so I probably want to at least preserve that for the
alias links, on the off-chance that someone cached one of them or that Google knows what my site is.

### Aliases and URLS

Normally, a content file is rendered to a URL that matches that of the `content` directory
location. This can be overriden with an attribute placed in the content file front matter.

This will create a "pretty" URL (e.g. a file `index.html` at the given path)

```toml
url = /2015/05/01/using-jekyll/
```

This will create an "ugly" URL

```toml
url = /2015/05/01/using-jekyll.html
```

Directly specifying URLs creates a maintenance issue, since the URLs are scattered across
many files. This is usually employed for migrating from other content systems. An option
is to let Hugo place files where it wants, and ask for aliases to be employed.

This will create a redirect file at `/2015/05/01/using-jekyll.html` that points to
the default location `post/2015-05-01-using-jekyll.html`. This is the best of both worlds,
in my opinion.

```toml
aliases = [ "/2015/05/01/using-jekyll.html" ]
```

Aliases ignore the `uglyurls` setting, so must be the complete path the alias.

## Styling pages with CSS

Simple CSS can be added via a stylesheet that is included in all pages.

Create a basic stylesheet in `static/css/style.css`. This will set a max width for
output text (to facitate readability), and put a box around code segments.

```css
body {
  max-width: 800px;
  margin: auto;
  padding: 1em;
  line-height: 1.25em;
}

pre {
  border: 1px solid #dddddd;
  box-shadow: 5px 5px 5px #eeeeee;
  line-height: 0.9em;
  padding: 1em;
  overflow-x: auto;
}
code { background: #ffecff; }
pre code { background: none; }

code {
  font-family: "Lucida Console", Monaco, monospace;
  font-size: 75%;
}
```

## Using a header and footer

Hugo uses the concept of partials to let you compose HTML from multiple files, instead of putting
all the code in single files. For example, you may wish to have a common header for a set of pages,
and by putting the common code in a partial, you can avoid copy-paste duplication. The usual reason
for this is to have a navigation menu that's used site-wide.

For now, we're just going to move the HTML boilerplate into two partials.

# Reference

[gohugoio/hugoThemes](https://github.com/gohugoio/hugoThemes). Themes created by the Hugo community.

[gohugoio/hugoBasicExample](https://github.com/gohugoio/HugoBasicExample). A test site to check new Hugo themes against.

[Zen](https://themes.gohugo.io/hugo-theme-zen/) - a clean starter theme.

[XMin](https://themes.gohugo.io/hugo-xmin/) - an even more minimal theme.
