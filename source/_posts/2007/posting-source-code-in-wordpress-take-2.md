---
layout: post
title: Posting Source Code in WordPress, Take 2
author: Daniel
date: 2007-06-13 11:33:15
categories:
- [ Programming, PHP, WordPress ]
tags:
- geshi
- plug-in
- source
- wordpress
- wp-syntax
summary: The WP Syntax Plugin is another plugin that simplifies posting source code
---

In my searching, I have found another WordPress source code plugin, called [wp-syntax][]. This one uses [GeSHi][], the Generic Syntax Highlighter. It features many languages, and is extensible to even more. (If I ever post a COBOL snippet, I'll probably add COBOL support to it, and contribute it to the codebase.)

To use it, you simply put a pre tag, followed by `lang=[language]`. It will also do line numbering. Of course, with a single "language" parameter, the embedded language will not be highlighted as well. In this case, the previous plug-in works better; although the syntax highlighting has to be done manually, it can handle multiple languages.

_(NOTE: The samples have been removed, as this blog is not running under WordPress. However, since the source code from another post was moved here, it is presented below, using [Jekyll][]'s PHP highlighting mode.)_

{% codeblock lang:php %}
<?php
/**
 * This creates a list of category links that can be used with a category dropdown
 */
$aCategories = get_all_category_ids();
$iMaxCat = 0;
foreach($aCategories as $iThisCat) {
    if ($iMaxCat < $iThisCat) {
        $iMaxCat = $iThisCat;
    }
}
$iMaxCat++;
?>
<div style="text-align:center;">
<form name="categoryform" action="" style="text-align:center;">
    <script type="text/javascript">
        var aLink = new Array(<?php echo($iMaxCat); ?>);
<?php
foreach($aCategories as $iThisCat) {
    echo("aLink[$iThisCat] = \"" . get_category_link($iThisCat) . "\";\n");
} ?>
        function goCat() {
            window.location =
                aLink[document.getElementById('cat')[document.getElementById('cat').selectedIndex].text;
        }
    </script>
    <?php wp_dropdown_categories('class=sidebardropdown&orderby=name&show_count=1&hierarchical=1'); ?>
    <br /><br />
    <button class="sidebarbutton" type="button" style="margin-top:5px;" onclick="goCat();">View Category</button>
    </form>
</div>
{% endcodeblock %}

This is another option, and is probably what I'll use for single-language posts, or posts where the embedded language may not be crucial.


[wp-syntax]: //wordpress.org/plugins/wp-syntax/ "WP Syntax Plugin"
[GeSHi]:     http://qbnz.com/highlighter/ "GeSHi"
[Jekyll]:    //jekyllrb.com "Jekyll"
