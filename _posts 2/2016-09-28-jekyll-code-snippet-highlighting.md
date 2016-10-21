---
date: '2016-09-28 13:57 +0200'
published: true
title: Jekyll Code snippet highlighting
category:
  - jek
---
Jekyll has built in support for syntax highlighting of over 60 languages thanks to Rouge. Rouge is the default highlighter in Jekyll 3 and above. To use it in Jekyll 2, set highlighter to rouge and ensure the rouge gem is installed properly.

Alternatively, you can use Pygments to highlight your code snippets. To use Pygments, you must have Python installed on your system, have the pygments.rb gem installed and set highlighter to pygments in your site’s configuration file. 

To render a code block with syntax highlighting, surround your code as follows:

```liquid
{% raw %}{%{% endraw %} highlight ruby {% raw %}%}{% endraw %}
def foo
  puts 'foo'
end
{% raw %}{%{% endraw %} endhighlight {% raw %}%}{% endraw %}
```

The argument to the highlight tag (ruby in the example above) is the language identifier. To find the appropriate identifier to use for the language you want to highlight, look for the “short name” on the [Rouge wiki](https://github.com/jneen/rouge/wiki/List-of-supported-languages-and-lexers) or the [Pygments’ Lexers page](http://pygments.org/docs/lexers/).

## Line numbers
There is a second argument to highlight called linenos that is optional. Including the linenos argument will force the highlighted code to include line numbers.

```liquid
{% raw %}{%{% endraw %} highlight ruby linenos{% raw %}%}{% endraw %}
def foo
  puts 'foo'
end
{% raw %}{%{% endraw %} endhighlight {% raw %}%}{% endraw %}
```

## Stylesheets for syntax highlighting
In order for the highlighting to show up, you’ll need to include a highlighting stylesheet. For an example stylesheet you can look at [syntax.css](https://github.com/mojombo/tpw/blob/master/css/syntax.css). These are the same styles as used by GitHub and you are free to use them for your own site. If you use linenos, you might want to include an additional CSS class definition for the .lineno class in syntax.css to distinguish the line numbers from the highlighted code.