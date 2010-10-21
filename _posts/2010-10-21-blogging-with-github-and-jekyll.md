---
layout: post
title: Blogging With Github & Jekyll
---
[jk]: http://jekyllrb.com/ "Jekyll"
[gh]: http://github.com/ "Github"
[wp]: http://wordpress.org/ "Wordpress"
[git]: http://git-scm.com/ "Git"
[tm]: http://macromates.com/ "TextMate"

This is the first of (hopefully) many blog posts to come in regards to some of the cool
technology tricks we've (Evan & I) picked up since beginning development here at
CollegeDegrees.com.

To kick it off, **we're blogging with [Github][gh] & [Jekyll][jk], not [Wordpress][wp]**.

- - -

## Why Not Wordpress?

[Wordpress][wp] (rightfully, in my opinion) powers the vast majority of blogs and
small-scale sites out there.  Several of our sites are no different.

However, our entire workflow revolves around using [Git][git] with [Github][gh]
(not to mention [TextMate][tm] & other tools, but that's another post),
so it's best to stick with what we know.

- - -

## Setting It Up

### Setup your Github Pages

Following [Github Pages Docs](http://pages.github.com/), we only had to create
a repo named `[username].github.com` (`collegedegrees.github.com`) with an `index.html`
file.

### Add CNAME support

All this required was:

    $ cd /path/to/collegedegrees.github.com
    $ echo "devblog.collegedegrees.com" > CNAME
    $ git add .
    $ git commit -a -m "Added CNAME"

then adding a `CNAME` record on our DNS host/registrar pointing to `collegedegrees.github.com`.

### Setting up Jekyll

It's best to actually preview your site while you're working on it, so running [Jekyll][jk]
locally involved simply [installing the gem](http://github.com/mojombo/jekyll/wiki/Install),
creating a `_config.yml` file...

{% highlight yaml %}
markdown: rdiscount     # Is a bit better away of context
pygments: true          # Useful for syntax highlighting
auto: true              # Re-generates site upon changes
{% endhighlight %}
    
...then starting the server:

    $ jekyll --server

- - -

## Adding Content

I was greatly inspired by [Mojombo's Blog](http://github.com/mojombo/mojombo.github.com/)
and [Rackspace's Customer Portal](https://my.rackspace.com/portal/home) for our blog.

Luckily, there are only a couple files at play here:

1. index.html (`/index.html`)
2. default.html (`/_layouts/default.html`)
3. post.html (`/_layouts/post.html`)
4. 2010-10-21-some-title.md (`/_posts/2010-10-21-some-title.md`)

[View the source code](http://gist.github.com/637950)

- - -

## Done!

We are using [Liquid Extensions](http://github.com/mojombo/jekyll/wiki/Liquid-Extensions)
to pull in Evan & I's twitter feeds as well as [DISQUS Comments](http://disqus.com).

All in all, we're very pleased with [Jekyll][jk] and [Github][gh] so far.
