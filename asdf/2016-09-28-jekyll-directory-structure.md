---
date: '2016-09-28 14:40 +0200'
published: true
title: Jekyll Directory Structure
category:
  - jek
---
Jekyll is, at its core, a text transformation engine. 

You give it text written in your favorite markup language, be that Markdown, Textile, or just plain HTML, and it churns that through a layout or a series of layout files. 

Throughout that process you can tweak how you want the site URLs to look, what data gets displayed in the layout, and more. A static web site is the final product.

A basic Jekyll site looks something like this:

```bash
.
├── _config.yml
├── _drafts
|   ├── begin-with-the-crazy-ideas.md
|   └── on-simplicity-in-technology.md
├── _includes
|   ├── footer.html
|   └── header.html
├── _layouts
|   ├── default.html
|   └── post.html
├── _posts
|   ├── 2007-10-29-why-every-programmer-should-play-nethack.md
|   └── 2009-04-26-barcamp-boston-4-roundup.md
├── _data
|   └── members.yml
├── _site
├── .jekyll-metadata
└── index.html
```

An overview of what each of these does:

|FILE / DIRECTORY|DESCRIPTION| 
|----------|:-------------:|
| _config.yml| Stores configuration data. Many of these options can be specified from the command line executable but it’s easier to specify them here so you don’t have to remember them. |
|_drafts|Drafts are unpublished posts. The format of these files is without a date: title.MARKUP. |
|_includes|These are the partials that can be mixed and matched by your layouts and posts to facilitate reuse.|
|_layouts|These are the templates that wrap posts. Layouts are chosen on a post-by-post basis in the YAML Front Matter|
|_posts|Your dynamic content, so to speak. The naming convention of these files is important, and must follow the format: YEAR-MONTH-DAY-title.MARKUP. The permalinks can be customized for each post, but the date and markup language are determined solely by the file name.|
|_data|Well-formatted site data should be placed here. The Jekyll engine will autoload all YAML files in this directory (using either the .yml, .yaml, .json or  .csv formats and extensions) and they will be accessible via `site.data`. If there's a file members.yml under the directory, then you can access contents of the file through site.data.members.|
|_site|This is where the generated site will be placed (by default) once Jekyll is done transforming it. It’s probably a good idea to add this to your .gitignore file.|
|.jekyll-metadata|This helps Jekyll keep track of which files have not been modified since the site was last built, and which files will need to be regenerated on the next build. This file will not be included in the generated site. It’s probably a good idea to add this to your .gitignore file.|
|index.html and other HTML, Markdown, Textile files|Provided that the file has a YAML Front Matter section, it will be transformed by Jekyll. The same will happen for any .html, .markdown,  .md, or .textile file in your site’s root directory or directories not listed above.|
|Other Files/Folders|Every other directory and file except for those listed above—such as  css and images folders,  favicon.ico files, and so forth—will be copied verbatim to the generated site.|

