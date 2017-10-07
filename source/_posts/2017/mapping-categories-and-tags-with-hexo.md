---
title: Mapping Categories and Tags with Hexo
author: Daniel
date: 2017-09-02 18:35:15
categories:
- [ Programming, JavaScript, Hexo ]
tags:
- category
- gitter
- hexo
- jekyll
- tag
- yaml
---
This blog moved today from [Jekyll][] to [Hexo][] (and it's now [open source][gh] as well). One of the final issues I had when wrapping up the conversion was how to handle categories and tags that do not necessarily have a slug that can be naturally derived from the name. Take the "C#" category, for example. The normal slug for that category would be `c`, which is an entirely different programming language; interestingly, "C++" will also normally get its slug as `c`.

Within Hexo's default `_config.yml` file, there are two empty items named `category_map` and `tag_map`; their comments allude to a mapping, but I could not find what the proper syntax was for those items. We hopped onto the Hexo [Gitter][] chat and asked the question, and someone pointed us to [this issue][iss]. To define a mapping, create an item under either the `category_map` or `tag_map` top-level item. The maps for this site, as they currently are, look like this:

{% codeblock _config.yml lang:yaml %}
category_map:
  C++: c-plus-plus
  C#: c-sharp
  .NET: dot-net
tag_map:
  c#: c-sharp
  .net: dot-net
{% endcodeblock %}

As you can see by hovering over the links in the sidebar, "Programming > .NET > C#" ends up with a URL ending with `/programming/dot-net/c-sharp/`, which is exactly what we were looking for.


[Jekyll]: //jekyllrb.com "Jekyll"
[Hexo]: //hexo.io "Hexo"
[gh]: //github.com/danieljsummers/blog.bitbadger.solutions "The Bit Badger Blog &bull; GitHub"
[Gitter]: //gitter.im "Gitter"
[iss]: //github.com/hexojs/hexo/issues/908#issuecomment-62913729 "Hexo category_map Issue"
