Siteleaf Theme Documentation
============================

- [Getting started](#getting-started)
- [Filters and Tags](#filters-and-tags)
- [Variables](#variables)
  - [Site](#site)
  - [Content (page, post, archive, taxonomy)](#content)
  - [Posts](#posts)
  - [Subpages](#subpages)
  - [Metadata](#metadata)
  - [Taxonomy](#taxonomy)
- [Naming your files](#naming-your-files)
- [Includes](#includes)
- [Contributing](#contributing)

Getting started
---------------

Siteleaf uses the popular [Liquid](https://github.com/Shopify/liquid) syntax for themes. If you can write HTML, you’ll have no problem using Liquid.

There are two types of markup in Liquid:

1) Output markup (which may resolve to text) is surrounded by
```html
{{ matched pairs of curly brackets (ie, braces) }}
```

2) Tag markup (which cannot resolve to text) is surrounded by
```html
{% matched pairs of curly brackets and percent signs %}
```

If you are new to Liquid, a good place to start is [Liquid for Designers](https://github.com/Shopify/liquid/wiki/Liquid-for-Designers).


Filters and Tags
----------------

Siteleaf supports all [Standard Liquid Filters](https://github.com/Shopify/liquid/wiki/Liquid-for-Designers#standard-filters) and [Liquid Tags](https://github.com/Shopify/liquid/wiki/Liquid-for-Designers#tags).

In addition, Siteleaf is compatible with [Jekyll Liquid Filters](http://jekyllrb.com/docs/templates/).


Variables
=========

Site
----

`site` contains global variables available to all pages.

Variable           | Description
--------           | -----------
`site.title`       | The title of your website.
`site.domain`      | Your website’s domain name (ie. `barlawrence.com`).
`site.pages`       | A nested array of pages.
`site.posts`       | Array of all posts in all pages.
`site.date`        | Date of most recent publish.

For example, use the following Liquid to get the title of your website:

```html
{{site.title}}
```

Loop through `site.pages` to build a menu:

```html
<nav>
  <ul>
  {% for page in site.pages %}
    <li><a href="{{page.url}}"{% if page.url == url %} class="selected"{% endif %}>{{page.title}}</a></li>
  {% endfor %}
  </ul>
</nav>
```

Content
-------

Variable           | Description
--------           | -----------
`type`             | Can be `page`, `post`, `archive`, `tagset`, or `tag`.
`title`            | Title of content.
`url`              | URL to object without domain (ie. `/blog/my-post`).
`permalink`        | Full URL to object with domain (ie. `http://mysite.com/blog/my-post`).
`body`             | Body in HTML (rendered from Markdown), available in `page` and `post` types.
`body_raw`         | Body in raw Markdown, available in `page` and `post` types.
`meta`             | Array of [metadata](#metadata), available on `page` and `post` types.
`taxonomy`         | Array of [taxonomy](#taxonomy), available on `post` type only.
`tags`             | Array of [tags](#tags), available on `tagset` type only.
`pages`            | Array of child-pages, available on `page` type only.
`posts`            | Array of posts, available in `page`, `archive`, and `tag` types.
`parent`           | Parent page object (if exists).
`date`             | Date of publish, available in `page` and `post` types.
`author.name`      | Full name of author, available in `page` and `post` types.
`author.firstname` | First name of author, available in `page` and `post` types.
`author.lastname`  | Last name of author, available in `page` and `post` types.
`author.email`     | Author’s email, available in `page` and `post` types.
`author.avatar`    | Author’s Gravatar URL, available in `page` and `post` types.

Get the title and body for the current page:

```html
<h2>{{title}}</h2>

{{body}}
```

Check for parent page:

```html
{% if parent %}
  <a href="{{parent.url}}">&larr; Back to {{parent.title}}</a>
{% endif %}
```


Posts
-----

See [Content](#content) for available variables.

Count the number of posts:

```html
This page has {{posts | size}} posts.
```

Loop through the first 20 posts on the current page:

```html
{% for post in posts limit:20 %}
<article>
  <header><a href="{{post.url}}">{{post.title}}</a></header>
  {{post.body}}
  <footer>Posted on {{post.date | date: "%b %d, %Y"}} by {{post.author.name}}</footer>
</article>
{% endfor %}
```


Subpages
-----

See [Content](#content) for available variables.

Count the number of subpages:

```html
This page has {{pages | size}} subpages.
```

Loop through the subpages:

```html
{% for page in pages %}
<article>
  <h3><a href="{{page.url}}">{{page.title}}</a></h3>
  {{page.body}}
</article>
{% endfor %}
```


Metadata
--------

Variable           | Description
--------           | -----------
`meta`             | Array of all metadata key/value pairs.
`meta.KEY`         | Get single metadata by key, ie. `Color`.

Count number of metadata fields:

```html
{{meta | size}}
```

Get metadata for key `Color`:

```html
{{meta.color}}
```

Get metadata for key `My Color`:

```html
{{meta['My Color']}}
```

Loop through metadata:

```html
<dl>
{% for data in meta %}
  <dt>{{data.key}}</dt> <dd>{{data.value}}</dd>
{% endfor %}
</dl>
```

Taxonomy, Tag Sets, & Tags
--------------------------

### Taxonomy:

Variable           | Description
--------           | -----------
`taxonomy`         | Array of all [tag sets](#tag-sets).
`taxonomy.KEY`     | Get single tag set by name, ie. `Tags`.

### Tag sets:

Variable           | Description
--------           | -----------
`key`              | Name of tag set, ie. `Tags`.
`slug`             | URI slug for tag set, ie. `tags`.
`url`              | URL for tag set page without domain, ie. `/blog/tags`
`permalink`        | URL for tag set page with domain, ie. `http://mysite.com/blog/tags`
`tags`             | Array of [tags](#tags) in tag set.
`tags.KEY`         | Get single tag by name, ie. `Design`.

### Tags:

Variable           | Description
--------           | -----------
`value`            | Name of tag, ie. `Design`.
`slug`             | URI slug for tag set, ie. `design`.
`url`              | URL for tag page without domain, ie. `/blog/tags/desgin`
`permalink`        | URL for tag page with domain, ie. `http://mysite.com/blog/tags/desgin`
`posts`            | Array of [posts](#posts) with this tag.

Count number of tags in the default `Tags` set:

```html
{{taxonomy.tags | size}}
```

Count number of tags in the `Colors` tag set:

```html
{{taxonomy.colors | size}}
```

Get first tag in the `Colors` tag set:

```html
{{taxonomy.colors.first}}
```

Loop through the `Tags` set:

```html
<ul>
{% for tag in taxonomy.tags %}
  <li><a href="{{tag.url}}">{{tag.value}}</a></li>
{% endfor %}
</ul>
```

Loop through the `Colors` set:

```html
<ul>
{% for tag in taxonomy.colors %}
  <li><a href="{{tag.url}}">{{tag.value}}</a></li>
{% endfor %}
</ul>
```

Loop through all tag sets:

```html
<ul>
{% for tagset in taxonomy %}
  <li>{{tagset.key}} ({{tagset | size}} tags)
    <ul>
    {% for tag in tagset %}
      <li><a href="{{tag.url}}">{{tag.value}}</a></li>
    {% endfor %}
    </ul>
  </li>
{% endfor %}
</ul>
```


Naming your files
=================

Siteleaf themes may contain one or more templates. 

Your base template should always be called `default.html`. 

Additional templates can be applied to other pages by following the same URL structure as your website:

URL               | Template
---               | --------
*                 | default.html (required, used by default)
/                 | index.html
/blog             | blog.html (or blog/index.html)
/blog/new-post    | blog/default.html
/blog/archive     | blog/archive.html (or blog/archive/index.html)


Includes
--------

Includes (or partials) can be added by using the `include` tag:

```html
{% include 'header' %}

{% include 'includes/menu' %}
```

Include files should be prepended with an underscore. In the above examples, Siteleaf will look for `_header.html` and `_includes/menu.html` respectively.


Contributing
============

Help us improve this documentation:

1. Fork it
2. Create your feature branch (`git checkout -b my-new-feature`)
3. Commit your changes (`git commit -am 'Add some feature'`)
4. Push to the branch (`git push origin my-new-feature`)
5. Create new Pull Request