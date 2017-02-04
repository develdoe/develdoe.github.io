---
date: '2016-09-30 10:55 +0200'
published: true
title: Jekyll Front Matter defaults
category:
  - Jekyll
---
Using YAML Front Matter is one way that you can specify configuration in the pages and posts for your site. Setting things like a default layout, or customizing the title, or specifying a more precise date/time for the post can all be added to your page or post front matter.

Often times, you will find that you are repeating a lot of configuration options. Setting the same layout in each file, adding the same category - or categories - to a post, etc. You can even add custom variables like author names, which might be the same for the majority of posts on your blog.

Instead of repeating this configuration each time you create a new post or page, Jekyll provides a way to set these defaults in the site configuration. To do this, you can specify site-wide defaults using the defaults key in the _config.yml file in your project’s root directory.

The defaults key holds an array of scope/values pairs that define what defaults should be set for a particular file path, and optionally, a file type in that path.

Let’s say that you want to add a default layout to all pages and posts in your site. You would add this to your _config.yml file:

```yml
defaults:
  -
    scope:
      path: "" # an empty string here means all files in the project
    values:
      layout: "default"
```

*The _config.yml master configuration file contains global configurations and variable definitions that are read once at execution time. Changes made to _config.yml during automatic regeneration are not loaded until the next execution.*
**Note that Data Files are included and reloaded during automatic regeneration.**

Here, we are scoping the values to any file that exists in the path scope. Since the path is set as an empty string, it will apply to all files in your project. You probably don’t want to set a layout on every file in your project - like css files, for example, so you can also specify a type value under the scope key.

```yml
defaults:
  -
    scope:
      path: "" # an empty string here means all files in the project
      type: "posts" # previously `post` in Jekyll 2.2.
    values:
      layout: "default"
```

Now, this will only set the layout for files where the type is posts. The different types that are available to you are pages, posts, drafts or any collection in your site. While type is optional, you must specify a value for path when creating a scope/values pair.

As mentioned earlier, you can set multiple scope/values pairs for defaults.

```yml
defaults:
  -
    scope:
      path: ""
      type: "posts"
    values:
      layout: "my-site"
  -
    scope:
      path: "projects"
      type: "pages" # previously `page` in Jekyll 2.2.
    values:
      layout: "project" # overrides previous default layout
      author: "Mr. Hyde"
```

With these defaults, all posts would use the my-site layout. Any html files that exist in the projects/ folder will use the project layout, if it exists. Those files will also have the page.author liquid variable set to Mr. Hyde.

```yml
collections:
  - my_collection:
      output: true

defaults:
  -
    scope:
      path: ""
      type: "my_collection" # a collection in your site, in plural form
    values:
      layout: "default"
```

In this example, the layout is set to default inside the collection with the name my_collection.

## Precedence
Jekyll will apply all of the configuration settings you specify in the defaults section of your _config.yml file. However, you can choose to override settings from other scope/values pair by specifying a more specific path for the scope.

You can see that in the second to last example above. First, we set the default layout to my-site. Then, using a more specific path, we set the default layout for files in the projects/ path to project. This can be done with any value that you would set in the page or post front matter.

Finally, if you set defaults in the site configuration by adding a defaults section to your _config.yml file, you can override those settings in a post or page file. All you need to do is specify the settings in the post or page front matter. For example:

```yml
# In _config.yml
...
defaults:
  -
    scope:
      path: "projects"
      type: "pages"
    values:
      layout: "project"
      author: "Mr. Hyde"
      category: "project"
...
```

```yml
# In projects/foo_project.md
---
author: "John Smith"
layout: "foobar"
---
The post text goes here...
```

The projects/foo_project.md would have the layout set to foobar instead of project and the author set to John Smith instead of Mr. Hyde when the site is built.

## Default Configuration
Jekyll runs with the following configuration options by default. Alternative settings for these options can be explicitly specified in the configuration file or on the command-line.

**Please note that both remove_block_html_tags and  remove_span_html_tags are currently unsupported in Jekyll due to the fact that they are not included within the kramdown HTML converter.**

```yml
# Where things are
source:       .
destination:  ./_site
plugins_dir:  _plugins
layouts_dir:  _layouts
data_dir:     _data
includes_dir: _includes
collections:
  posts:
    output:   true

# Handling Reading
safe:         false
include:      [".htaccess"]
exclude:      []
keep_files:   [".git", ".svn"]
encoding:     "utf-8"
markdown_ext: "markdown,mkdown,mkdn,mkd,md"

# Filtering Content
show_drafts: null
limit_posts: 0
future:      false
unpublished: false

# Plugins
whitelist: []
gems:      []

# Conversion
markdown:    kramdown
highlighter: rouge
lsi:         false
excerpt_separator: "\n\n"
incremental: false

# Serving
detach:  false
port:    4000
host:    127.0.0.1
baseurl: "" # does not include hostname
show_dir_listing: false

# Outputting
permalink:     date
paginate_path: /page:num
timezone:      null

quiet:    false
verbose:  false
defaults: []

liquid:
  error_mode: warn

# Markdown Processors
rdiscount:
  extensions: []

redcarpet:
  extensions: []

kramdown:
  auto_ids:       true
  footnote_nr:    1
  entity_output:  as_char
  toc_levels:     1..6
  smart_quotes:   lsquo,rsquo,ldquo,rdquo
  input:          GFM
  hard_wrap:      false
  footnote_nr:    1
```