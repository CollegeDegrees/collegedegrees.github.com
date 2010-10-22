---
layout: post
title: 5 Tips Learned From Enterprise Development
---

## Preface

We've recently begun the hunt for experienced PHP developers down here in Houston.
For having an overwhelmingly low amount of open-source talent (good luck finding a SSJS or
NodeJS meetup group down here!), we've stumbled upon some pretty good candidates (with
the help of recruiters, of course).

I'd like to think that Evan & I have done an excellent job of adopting best-practices and the
latest tools & methodologies, so we like to review some of our older code (3 years or less) when
reviewing potential hires.

Why? Because not everyone has worked in an environment that...

* ...runs primarily on Linux.
* ...allows contributions/use of open-source projects (sometimes because of license).
* ...allows the developer to research before implementing.
* ...encourages refactoring & code reviews.
* ...sets priorities, but not strict timelines, on projects.

With that being said, some of the little things we've learned are the following...

## You're Not "Smarter" Than Everyone Else

It seems obvious, but being *smarter* usually causes a developer to unknowingly re-invent a
design pattern or an entire framework.  Exposing that persistent `$dbConn` that you
instantiated in `index.php` as a global is nothing more than a hacky Singleton.

Often times, being *smarter* may cause a developer to forego using a solution that would
accomplish 90% of a project because of it's coding style or API/interface.  Which leads me to...

## Go Where the Crowd Is

There's a reason why many mobile-platform developers because developing for the iPhone despite
vowing to avoid it initially: **that's where the crowd is** (for now).

Nowadays, professional programming is less about boiler-plate code and instead about knowing
which tools to use and when.  How do you know which tool to use?  Find several that suit your
needs then whittle them down based on **community**, **documentation** and which are under
**active development**.

Zend Framework has a very large, active community behind it that are adamant about improving
it's core and adding niche features.  Dojo also has a large (mostly European, from what I can
tell) community that's fairly active, but pails in comparison to jQuery in the US.

Which is why **documentation matters**.  Dojo has had notoriously difficult to use/find
documentation, particularly when venturing outside of the public API and having to use private
methods to get the functionality you need.  This speaks to it's complexity as a full-stack
framework and why jQuery, in its simplicity, has very concise, simple documentation & examples.

Lastly, I've found that relying on a tool that is **actively developed** by an author or
contributors has a much better chance that it's quality will improve over time & any bugs I
stumble across have a chance at being fixed by either the author or myself (thanks to
[Github](http://github.com)).

Actively developed tools also tend to follow the latest & greatest best practices...

## Clarity > Magic

We've all developed classes before that contain several properties, such as the typical
`User` model:

{% highlight php %}
class User
{
    protected $id;
    protected $firstName;
    protected $lastName;
    ... 40 more properties ...
    protected $zebraOwner
}
{% endhighlight %}

So, to keep things *DRY*, you create a `__call($method, $args)` function to
automatically act as the getters & setters `getFirstName()`, `setFirstName('Eric')`, etc..

If this was all the functionality that would ever exist, then perhaps that or
using [ArrayAccess](http://php.net/manual/en/class.arrayaccess.php) would be fine.

But, you lose a lot of clarity as to what's going on behind the scenes because
of these *magic methods* that makes negatively affect debugging, maintenance
& code quality.  Take a look at [Zend\View\Helper\HeadScript](http://github.com/zendframework/zf2/blob/master/library/Zend/View/Helper/HeadScript.php#L190),
for example.  Working a class who's public methods are so simple has suddenly become
complex and confusing.

The solution?  Forgetting about *DRY* code for redundant, but *clear*, code.

I believe [Doctrine2](http://github.com/doctrine/doctrine2) has done a good job of this,
as indicated in [Entities\User](http://github.com/doctrine/doctrine2/blob/master/tools/sandbox/Entities/User.php).

Every property has its own getter & setter.  Redundant?  Yes.  Flexible?  You betcha.

Now your `setOptions` method can accept an `Array`, `StdObject` or even a `Zend_Config` instance.
The moment you run into a problem or want to add more functionality, you know exactly where to go.

## Build the API & Domain Models

I've gone through the entire development life-cycle on several web applications
myself (Server, Database, Back-End, Middleware, Front-End, QA, Test, Launch) and
can't definitively say what's the best strategy for building out a new application.

But, in every recent scenario, taking the time to step away from a code editor
and defining the business/domain models & logic that encompasses the application
will go a long ways to improving the quality, understanding & maintainability of
your application.

I've become a fan of creating a (sometimes public-facing) REST API for an application,
especially when using PHP.  As PHP is mostly stateless, an API acts as a guide to
creating atomic entry-points into the application for fetching & aggregating specific
data.  When you need to expose portions of your application or data to a customer,
you've already done the work.  "Eating your own dog food" only helps to ensure the
quality of your code.

We've moved from having a large, complicated application to several small, focused
apps that communicate via their respective APIs.  The performance hit has been
negligible, especially with bits of caching via *memcached*.

## Own Your Environment & Application

There have been past debates in the PHP community about whether or not
short tags (&lt;? ?&gt; & &lt;?= ?&gt;) should be allowed or defaulted.

For Zend Framework and other tools, portability says "no".  But, for your
application & your code, use whatever's best & easiest to work with.

Your server environment should be a near-mirror of your staging, QA, testing
and development environment.  In the same way, your application code should
hardly change between environments.

Zend Framework applications, for example, pivot off of environment once: in
`index.php` where the `APPLICATION_ENV` constant defines which rules to use
in `Zend_Config`.

Developing on Windows & pushing to a Linux shared-host?  That's asking for
extra work.

Cloud instances & VPS machines have become so cheap that every serious
developer can afford to develop in a local VM that matches their production
environment.  So when you want to switch to using *MongoDB* for your database,
you'll know exactly what it takes to get up & running with less surprises.

## Conclusion

These are largely theoretical with a bit of code to illustrate my opinions,
but if you're curious in how to put a lot of these concepts into practice,
just follow some of the major tools/frameworks for your chosen language:

**PHP**

* [Doctrine](http://github.com/doctrine/doctrine)
* [Zend Framework](http://github.com/zendframework/zf2)

**Server-Side Javascript**

* [NodeJS](http://nodejs.org/)
* [ExpressJS](http://expressjs.com/)

etc.