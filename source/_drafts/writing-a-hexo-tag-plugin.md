---
layout: post
title: Writing a Hexo Tag Plugin
date: 2017-09-09 23:45:17
categories:
- [ Programming, JavaScript, Hexo ]
tags:
- bible
- hexo
- jekyll
- plugin
- tag
---
In the process of migrating [Daniel's Weekly Devotions][dwd] to [Hexo][], we ran into a problem that we felt sure a tag plugin could solve.

## The Problem

[Jekyll][]'s Markdown parser follows the original one, where text within an HTML tag is not processed. This can be the desired behavior in many cases, as you could put what would otherwise be translated as Markdown between HTML tags, and the parser/renderer will leave it alone. One of the common features, used multiple times in most posts, are links to Scripture references and blocks of quoted text. We had an `include` to automate the links, but we needed a special class on the `<blockquote>` tag, which meant that all Scripture blockquotes could not use Markdown (or end up with "smartified" quotes and such; we had to use the HTML entities within these quotes.) We also included the verse numbers as superscripts within the quoted text; more tags.

It looked something like this... _(the "ref" CSS class turns the text red)_

{% codeblock lang:html %}
<blockquote class="bible">
  <p>
    <sup>11</sup> &hellip;And Jesus said, <span class="ref">&ldquo;Neither do I condemn you;
    go, and from now on sin no more.&rdquo;</span>
  </p>
  <cite>&mdash; <a href="https://www.biblegateway.com/passage/?search=John+8:11&amp;version=ESV"
    title="Read John 8:11 (ESV) at Bible Gateway">John 8:11</a>b <em>(ESV)</em></cite>
</blockquote>
{% endcodeblock %}

If you've ever edited Markdown, you'll recognize how jarring all that HTML code is within the flow of the otherwise-regular text; and look at all those entities!

## The Solution

We looked through at the [Hexo Plugin List][pl] to find some examples, and began working towards writing a plugin to handle both the links (the part within the `<cite>` in the example above) as well as the entire blocks of quoted text. Some tags, like the `{% raw %}{% codeblock %}{% endraw %}` tag, have a start tag and an end tag (`{% raw %}{% endcodeblock %}{% endraw %}`); others, like the `{% raw %}{% youtube %}{% endraw %}` tag, just pass arguments with the tag. _([You can see all the default tags here][tag].)_ Hexo passes two arguments to the tag plugin - the arguments within the (start) tag, plus the content (which is blank for tags that don't have an end tag). The returned value from the plugin call is substituted in the document.

For generating a link, that is pretty easy; it could be an inline tag, and it's just a matter of parsing the arguments and forming a link. For the quotes, we need to make sure that we include the content, and Hexo provides a way to run that content through the Markdown renderer. We are converging on a solution!

Hexo will pick up and execute any `.js` files in the `scripts` directory of the site as its generating it, so the first efforts were just local to that repo. The reference link looked something like this...

{% codeblock esv.js lang:javascript %}
hexo.extend.tag.register('esv', (args, content) => {
  // option parsing with RegEx, similar to the way their tags do

  let reference = arg.trim()
  let urlReference = reference.split(' ').join('+')

  return `<a href="https://www.biblegateway.com/passage/?search=${urlReference}&amp;version=${version}" `
    + `title="Read ${reference} (${version}) at Bible Gateway">${reference}</a>${extraText}${versionText}`
})
{% endcodeblock %}

...which let the Markdown document go from...

{% codeblock lang:html %}
<a href="https://www.biblegateway.com/passage/?search=John+8:11&amp;version=ESV"
  title="Read John 8:11 (ESV) at Bible Gateway">John 8:11</a>b <em>(ESV)</em>
{% endcodeblock %}

...to...

{% codeblock lang:markdown %}
{% raw %}{% esv John 8:11 extra:b show-version %}{% endraw %}
{% endcodeblock %}

We refactored the link code to be version-agnostic and extracted it from the `tag.register` function so that we could reuse that for the blockquote citation. This made the local version of the blockquote look something like this:

{% codeblock bible.js lang:javascript %}
hexo.extend.tag.register('bible', (args, content) => {
  let text = hexo.render.renderSync({ text: content, engine: 'markdown' })
  return `<blockquote class="bible">${text}<cite>&mdash; ${generateRef(args)}</cite></blockquote>`
})
{% endcodeblock %}

This means that the blockquote can support all of the arguments the inline reference did. We also switched out the `marked` Markdown processor for the `markdown-it` one, which lets us do superscripts by using the `^` character. Revisiting our example under "The Problem," our Markdown source to generate the same blockquote is now:

{% codeblock lang:markdown %}
{% raw %}{% bible John 8:11 extra:b show-version %}{% endraw %}
^11^...And Jesus said, <span class="ref">"Neither do I condemn you; go, and from
now on sin no more."</span>
{% raw %}{% endbible %}{% endraw %}
{% endcodeblock %}

## The Plugin

The plugin is available on [npm][], is fully [tested][], and its source is [open][]. If you use Hexo, and wish to cite Scripture references in your posts with links where readers can see the text for themselves - enjoy!


[dwd]: //devotions.summershome.org
[Hexo]: //hexo.io
[Jekyll]: //jekyllrb.com
[pl]: //hexo.io/plugins/
[tag]: //hexo.io/docs/tag-plugins.html
[npm]: //www.npmjs.com/package/hexo-tag-scripture
[tested]: //ci.appveyor.com/project/danieljsummers/hexo-tag-scripture
[open]: //github.com/danieljsummers/hexo-tag-scripture
