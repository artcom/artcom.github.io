---
layout: post
title: 'A simple Jekyll include snipped to embed Youtube Videos'
videoId: 8FcfJadYysk
excerpt_separator: <!--end-of-excerpt-->
---

As you might know, you can't have plugins beyond the ones pre-defined by github when you are serving your pages as github pages. So you can't add a plugin to handle the youtube video embedding. Luckily it is easy to build a small html snipped and use it as include file. Here you go:

{% include youtube.html id=page.videoId %}
<!--end-of-excerpt-->

In you markdown page, in source, the above video looks like this:

{% highlight html %}
{% raw %}
{ include youtube.html id="8FcfJadYysk" %}
{% endraw %}{% endhighlight %}

which becomes than rendered as:
```html
{% include youtube.html id="8FcfJadYysk" %}
```
