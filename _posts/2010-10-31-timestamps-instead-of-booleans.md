---
layout: post
title: Timestamps Instead of Booleans?
published: true
---

## Preface

In many of our database tables, we have columns that are slightly redundant.  For example, a
blog post may have an `is_published` and a `published_date` column.  This isn't really a problem
until you explore Documented-based Databases (NoSQL) where length of a column name as well as the
total number of columns have an impact on your database size.

## Bad - Change the Application Code

One approach is to lose some clarity in the application and an extra column by simply basing the `is_published` value
by the `published_date`:

{% highlight php %}
// Retrieve a `Post` object
$post = Posts::findById($id);

$pubDate = $post->getPublishedDate();

if ($pubDate) {
    echo "Published: " . date($pubDate, 'Y-m-d H:i:s');
} else {
    echo "Post is not yet available.";
}
{% endhighlight %}

## Good - Change the Model Code

Building off of the first example, we can clean up our code a little bit better and add clarity to
our rendering logic...

{% highlight php %}
$post = Posts::findById($id);

if ($post->isPublished()) {
    echo "Published: " . date($post->getPublishedDate(), 'Y-m-d H:i:s');
} else {
    echo "Post is not yet available.";
}
{% endhighlight %}

by updating the `Post` model instead...

{% highlight php %}
class Post
{
    
    protected $date_published;
    
    ...
    
    public function getPublishedDate()
    {
        return $this->date_published;
    }
    
    public function isPublished()
    {
        return !!$this->getPublishedDate();
    }
    
    ...
    
}
{% endhighlight %}

- - -

## Summary

I'm not entirely sure how I feel about this approach.  Perhaps in a NoSQL world it may make sense,
but I've become accustomed to having less & less *magic* in code and being more and more *explicit*.

When persisting entities, a *DataMapper* will do just fine with `$post->setIsPublished(false)`, so
it truly comes down to personal preference and application needs.

## Signed Integers Instead?

With the above approach, you can only tell when a post is published and not when it was disabled, as
the *disabled* state is stored as a `0` or `NULL`.

What about storing a Timestamp as a Signed Integer for the *enabled*/*disabled* state?
For example, `enabled_disabled_date` could be `±1288561512`.  When *positive*, you infer the
`published_date`.  When *negative*, you infer the `disabled_date`.

Ultimately, you're adding/removing data based on state, so you won't be able to find the
*last published date* on a disabled post.