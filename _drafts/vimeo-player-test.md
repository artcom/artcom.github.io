---
layout: post
title: 'Manta Rhei as Vimeo Video Player Test'
excerpt_separator: <!--end-of-excerpt-->
---
Showing the Manta Rhei kinetic light fixture to test the vimeo jekyll include on this blog. 

{% include vimeo.html id="46289124" %}

<!--end-of-excerpt-->

The markdown for the video embed looks like this:

{% highlight html %}
{% raw %}
{% include vimeo.html id="46289124" %}
{% endraw %}{% endhighlight %}

The above markdown gets rendered by the jekyll template processing and becomes transformed to this static HTML:
```html
{% include vimeo.html id="46289124" %}
```

## Youtube

Of course, such an include snipped can be build for youtube as well:

From here:

{% highlight html %}
{% raw %}
{% include vimeo.html id="46289124" %}
{% endraw %}{% endhighlight %}

to there:

```html
{% include youtube.html id="8FcfJadYysk" %}
```

to this:

{% include youtube.html id="8FcfJadYysk" %}
