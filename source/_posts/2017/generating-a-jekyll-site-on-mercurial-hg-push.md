---
layout: post
title: Generating a Jekyll Site on Mercurial (Hg) Push
author: Daniel
date: 2017-02-18 12:41:00
categories:
- Linux
- [ Programming, Jekyll ]
tags:
- commit
- cron
- hg
- jekyll
- mercurial
- push
- regenerate
- rsync
- scp
- ssh
summary: The process we use to regenerate Jekyll sites when a push occurs to a private Mercurial (Hg) repository
---

As we mentioned in [our last post][v4], we plan to share aspects of how we moved to [Jekyll][]. This is the first of these posts.

## Background

With a database-based solution, updating is easy; when the user updates content through the user interface, the new content is served when the page or post is requested. However, with a static site, an "update" is technically any change to the underlying files from which the site is generated. Typically, though, this is marked by source control commit and push to a master repository. [GitHub Pages][ghp], the product for which Jekyll was developed, uses this as a flag to regenerate the site. We weren't using GitHub*, though - we were using [Mercurial][hg] (Hg) for our source code control, with the master repository on a different server than the one from which the site is served.

_\* There were a few reasons we did not wish to host our sites using GitHub, none of which are pertinent to how this works._

## Options

With the need to regenerate the site after each site's master repository receives a push, there were a few different options we considered.

1. When a push occurs, regenerate the site on the Hg server, then use `scp` to delete the old files from and copy the new files to the web server.
2. Set up a sandbox on the Hg server that updates and regnerates each time a push occurs, and run `rsync` on the web server to check for updates every so often.
3. When a push occurs, notify the web server, and have it regenerate the site.

The first option has the potential to run afoul of SSH rate limits, plus has the potential to require much more data transfer than option 3.  The second option had the advantage of running a process local to the Hg server, but would have required disk space utilization that we didn't really need; and, as Jekyll regenerates all the pages in a site, `rsync` would have likely ended up transferring all the data for every update anyway, losing one of its benefits. The third option required Jekyll to be installed on the web server, and uses it for processing, potentially taking cycles that could be used to serve web pages.

Eventually, we decided to go with option 3.

## Script All the Things

On the Hg server, in the master repository for each site, we put the following in `.hg/hgrc` _(the following examples are for this site)_:

{% codeblock hgrc lang:shell %}
[hooks]
incoming = /opt/jobs/notify.tech-blog.sh
{% endcodeblock %}

...and then, `notify.tech-blog.sh`...

{% codeblock notify.tech-blog.sh lang:shell %}
#!/bin/bash
ssh user@web.server touch /opt/jobs/jekyll/.tech-blog
{% endcodeblock %}

That is the only logic required on the Hg server. Now, over on the web server, we need logic to regenerate the site and make it live. Since we have multiple sites, we wrote a script that has a few variables, so it could be duplicated for other sites. The following is `tech-blog.sh`:

{% codeblock tech-blog.sh lang:shell %}
##
## DJS Consulting Tech Blog Jekyll Build Script
##
## This will check out, build, and replace a Jekyll-generated site. Just update
## the parts under the "Env" heading for the specific site.
##

## Env
REPO=jekyll.tech-blog
DEST=/path/to/public/techblog
TRIGGER=.tech-blog
## /Env

cd /opt/jobs/jekyll

if [ -e $TRIGGER ]
then
  rm $TRIGGER

  ## Check out the site and build it
  hg clone ssh://user@hg.server/HgPath/$REPO $REPO
  cd $REPO
  jekyll build

  ## Copy it to the proper directory
  cd _site
  rm -r $DEST/*
  cp -r * $DEST

  ## Clean up
  cd ../..
  rm -r $REPO
fi
{% endcodeblock %}

This script isn't perfect; it needs to check the exit code from the Jekyll build process before whacking the current site (and notifying for a failed build would be a nice addition). However, with Jekyll being the same on both development and production, and a single committer, this is fine for our purposes.

Finally, each script needs to be run to check for the presence of the semaphore (or `TRIGGER`, as the script calls it). The following cron definition will check every 4 minutes for a change.

{% codeblock crontab lang:shell %}
*/4 *   *   *   *    /opt/jobs/jekyll/tech-blog.sh > /dev/null
{% endcodeblock %}

## Conclusion

Overall, we're pleased with the results. The inter-server communication is light, only requiring one initiated `ssh` connection from each server, so we won't run afoul of rate limits. With the work being done on the destination server, the amount of time where there are no files in the directory (between the `rm -r $DEST/*` and the time the `cp -r * $DEST` finishes) is very short; it would have been much longer if the directory were being repopulated across the network, or more complex if we added a staging area on the web server. Each piece can be run separately, and if we've committed a post with a future date, we can run the same `touch` command to make that post appear.

Next time, we'll discuss our experiences converting a non-WordPress site.


[v4]:     /2017/tech-blog-v4.html "Tech Blog v4 &bull; The Bit Badger Blog"
[Jekyll]: //jekyllrb.com "Jekyll"
[ghp]:    //pages.github.com "GitHub Pages"
[hg]:     //www.mercurial-scm.org "Mercurial (Hg)"
