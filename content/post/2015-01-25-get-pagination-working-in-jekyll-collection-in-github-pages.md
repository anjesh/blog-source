---
title: Get Pagination working in Jekyll Collection in Github pages
author: ''
date: '2015-01-25'
slug: get-pagination-working-in-jekyll-collection-in-github-pages
categories: []
tags: []
---

TL;DR

Pagination doesn't work in jekyll collections by default. By pagination i meant getting the links and title of previous and next links, because that's our requirement. Few lines of complicated looking code got what we are looking for. Jump to the code below or grab the [github repo](https://github.com/pagination-demo/pagination-demo.github.io) or see [the working demo](http://pagination-demo.github.io/tripcollection/trip-1/).

<hr>

Pagination works by default for the posts in github pages. There are plugins to make it work if you are hosting jekyll site other than github. 

If you are in any post (under [_post folder](https://github.com/pagination-demo/pagination-demo.github.io/tree/master/_posts)), then `page.previous` and `page.next` works out of the box. With the following code, you may easily get the url, title of the previous and next posts and display accordingly. 

```html
{% raw  %}
<div id="post-nav">
    <div >    
        {% if page.previous.url %}
        <a class="prev" href="{{page.previous.url}}">
            <span>&lt; {{page.previous.title}}</span>
        </a> 
        {% endif %} 
        {% if page.next.url %} 
        <a class="next" href="{{page.next.url}}">
            <span>{{page.next.title}} &gt;</span>
        </a> 
        {% endif %} 
    </div>
</div>
{% endraw  %}
```

See [post pagination demo here](http://pagination-demo.github.io/2013/12/31/post-1/)

From [Jekyll site](http://jekyllrb.com/docs/pagination/)

> Pagination pages through every post in the posts variable regardless of variables defined in the YAML Front Matter of each. It does not currently allow paging over groups of posts linked by a common tag or category. It cannot include any collection of documents because it is restricted to posts.

The above code doesn't work if you are working on collection entries. 

I enabled the collection in the `_config.yml` and created the folder `_tripcollection` in the root.

```yaml
collections:
  tripcollection:
    output: true
    permalink: /:collection/:title/
{% endhighlight %}
```

I added posts in the [tripcollection folder](https://github.com/pagination-demo/pagination-demo.github.io/tree/master/_tripcollection)  and added the following code to get the previous and next links in the collection post in the [trip layout file](https://github.com/pagination-demo/pagination-demo.github.io/blob/master/_layouts/trip.html).

```html
{% raw  %}
{% for c in site.tripcollection %}
{% if c.title == page.title %}
  {% assign thisPost = c %}
  {% if forloop.index == 1 %}
    {% assign prevflag = 0 %}
    {% assign nextflag = 1 %}
  {% elsif forloop.index == forloop.length %}
    {% assign prevflag = 1 %}
    {% assign nextflag = 0 %}
  {% else %}
    {% assign prevflag = 1 %}
    {% assign nextflag = 1 %}
  {% endif %}  
{% endif %}
{% endfor %}

{% for c in site.tripcollection %}
  {% if c.title == page.title %}
    {% assign prevflag = 0 %}
  {% endif %}
  {% if prevflag == 1 %}
    {% assign prevPost = c %}
    {% assign page.previous = c %}
  {% endif %}
{% endfor %}

{% if nextflag == 1 %}
  {% for c in site.tripcollection %}
    {% if foundPost == 1 %}
      {% assign getNext = 1 %}
    {% endif %}
    {% if c.title == page.title %}
      {% assign foundPost = 1 %}        
    {% endif %}
    {% if getNext == 1%}
      {% assign nextPost = c %}
      {% assign page.next = c %}
      {% assign foundPost = 0 %}
      {% assign getNext = 0 %}
    {% endif %}
  {% endfor %}
{% endif %}

<div id="post-nav">
    <div >    
        {% if prevPost.url %}
        <a class="prev" href="{{prevPost.url}}">
            <span>&lt; {{prevPost.title}}</span>
        </a> 
        {% endif %} 
        {% if nextPost.url %} 
        <a class="next" href="{{nextPost.url}}">
            <span>{{nextPost.title}} &gt;</span>
        </a> 
        {% endif %} 
    </div>
</div>
{% endraw  %}
```

I see you raised your eyebrows looking at the code above. It works and got it working after number of hit and trials. It's just pure logic, it looked complicated because of lack of language constructs in liquid template.

Now you get previous and next links in your collection posts as well. See the [working demo here](http://pagination-demo.github.io/tripcollection/trip-1/).

You may download the working code from the [github](https://github.com/pagination-demo/pagination-demo.github.io).
