---
date: '2016-09-30 11:11 +0200'
published: true
title: Jekyll Markdown Options
category:
  - jek
---
The various Markdown renderers supported by Jekyll sometimes have extra options available.

## Redcarpet
Redcarpet can be configured by providing an extensions sub-setting, whose value should be an array of strings. Each string should be the name of one of the Redcarpet::Markdown class’s extensions; if present in the array, it will set the corresponding extension to true.

Jekyll handles two special Redcarpet extensions:

* **no_fenced_code_blocks** — By default, Jekyll sets the fenced_code_blocks extension (for delimiting code blocks with triple tildes or triple backticks) to true, probably because GitHub’s eager adoption of them is starting to make them inescapable. Redcarpet’s normal fenced_code_blocks extension is inert when used with Jekyll; instead, you can use this inverted version of the extension for disabling fenced code.

**Note that you can also specify a language for highlighting after the first delimiter:**

```html
 ```ruby
    ...
 ```
```

With both fenced code blocks and highlighter enabled, this will statically highlight the code; without any syntax highlighter, it will add a class="LANGUAGE" attribute to the <code> element, which can be used as a hint by various JavaScript code highlighting libraries.

* **smart** — This pseudo-extension turns on SmartyPants, which converts straight quotes to curly quotes and runs of hyphens to em (---) and en (--) dashes.

All other extensions retain their usual names from Redcarpet, and no renderer options aside from smart can be specified in Jekyll. A list of available extensions can be found in the Redcarpet README file. Make sure you’re looking at the README for the right version of Redcarpet: Jekyll currently uses v3.2.x. The most commonly used extensions are:

* tables
* no_intra_emphasis
* autolink