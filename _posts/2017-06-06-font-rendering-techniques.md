---
layout: post
title: Browsers as Font Renderers
author: <a href="https://github.com/DarkMio/">Martin (Mio) Zier</a>
excerpt_separator: <!--end-of-excerpt-->
---

Font rendering is a difficult topic in general. Unicode is trying to set an
uniform standard juggling all kinds of glyphs and - which is often forgotten - 
control characters for different styles of writing.

![]({{site.url}}/images/2017-05-08-font-rendering-techniques/unicode.png)

<!--end-of-excerpt-->

That said, a modern font rendering should have a lot of capabilities:
- broad support of unicode
- fallback fonts for unknown glyphs by the used font
- ability to render in different writing directions
- support for basic layouting, kerning, line heights, font styles
- basic support for continous font blocks and text flow
- additional font styling, like outlining, shadowing and letter casing

That is a lot of work to get right. The probably best, commercially available
software doing this is probably Adobe InDesign, but luckily there are free,
heavy duty font renderers out there with a familiar layouting markup: Browsers!




# Idea

Unity is a great engine and works well for our projects at ART+COM. However, 
layouting and rendering fonts is definitly one of its weak points. In one
meeting we had this unique idea to stuff a lot of data into a headless browser
and it returns an image as result. This way we could use all features browsers
provide and have very extensive rendering of dynamic content


# Tools and considerations

### What is a headless browser?

Basically a browser without a a graphical interface - but with some kind of API.
There are a bunch of headless browsers out there, all of them are mostly used
for automatic unit testing on a final webpage, so to say the final website test
before deploying a setup.

### Considerations

You will find a good list of headless browsers on [@dhamaniasad repository], it
even include the very recently added headless Chrome / Chromium, which you can
use if you're running a Chrome Beta Browser.

We considered these browsers:

- [Splash - JS Rendering Service]:
  basically exactly what we were looking for - but needing additional
  dependencies on Python and Docker, which was no option for the project this
  was built for
- [Awesomium]:
  we've built applications with integrated Awesomium in Unity and it was great -
  unfortunately development seems to have ceased at some point and is
  considered dead now
- [SlimerJS]:
  Requires an installation of Firefox to run - probably the right tool for many
  things, yet not feasible in our case
- [Chromium Headless]:
  It just released as a buggy beta feature. Currently there is only support 
  and remote access by the [remote debugging protocol] for Chrome. Not yet
  production ready.

### Setup
- [PhantomJS] running with its [Web Server Module]
- The WebServer is driven by a Express-like API, which was forked and modified 
  from [here]
- Local development is driven by [nodemon], which is a convenient file watcher
  that restarts PhantomJS when a filechange occured allowing an iterative
  development process

### Advantages
- HTML, CSS and even JS out of the box - together they form a good foundation
  with a huge eco system for layouting
- very quick - for the amount of work that a browser can do
- media designers can prototype UI elements in a browser - the integration into
  the engine is linked with minor overhead
- headless browser is running as a service - different processes on that machine
  can use the same service to render text
- PhantomJS in particular is a single binary with all dependencies bundled,
  great as portable software

### Disadvantages
- Our current solution is definitly **not** safe - it never should be able to
  talk to any remote devices outside the local system boundaries
- Adding to the previous statement: There is currently no sanitization, as it
  was not a priority at the current state
- The headless browser is occasionally leaky, requiring a watchdog to restart
  the service on a daily basis
- Interactive UIs are certainly possible, but require additional work and 
  render times are at around 50 - 150ms per request
- PNG output for transparent images, which requires additional work inside the
  target engine to enable proper alpha-textures, including possible memory
  overhead by unoptimized texture patches
- Having such a tool (figuratively a hammer) makes a lot of problems look like
  a nail - even if it makes no sense to slam it on top of every problem

[@dhamaniasad repository]: <//github.com/dhamaniasad/HeadlessBrowsers>
[Splash - JS Rendering Service]: <//github.com/scrapinghub/splash>
[Awesomium]: <//www.awesomium.com/>
[SlimerJS]: <//slimerjs.org/>
[Chromium Headless]: <//chromium.googlesource.com/chromium/src/+/lkgr/headless/README.md>
[remote debugging protocol]: <//chromedevtools.github.io/devtools-protocol/>
[PhantomJS]: <//phantomjs.org/>
[Web Server Module]: <//phantomjs.org/api/webserver/>
[nodemon]: <//github.com/remy/nodemon>
[here]: <//gist.github.com/trevordixon/3061477>


# Goals & Technique 

As a student intern my main goal was to have a portable and extensible REST API
on PhantomJS, allowing for rapid development of new API endpoints with a
somewhat sane management of routes and logic - all while exploring how PhantomJS
could serve a purpose as font renderer.

Our basic setup is straight forward: PhantomJS gets hosted and responds to a
REST API, Unity Engine sends requests to that API and either receives an URL
or an image - depending on what behaviour is preferred.

## REST Routing
Again, PhantomJS is not really meant to be used as a webservice - even writing
a simple file server is not a simple task of a highly abstracted libraries like 
[NodeJS does have].  
Note that PhantomJS only allows for ``GET, POST, PUT, HEAD, DELETE`` http
methods and will reply with an error on all other methods.

Since I am very familiar with ExpressJS and it is a good and reasonable workflow
for REST APIs, I set my mind to ventures to a simliar API that allows for
effective routing and a readable syntax. After juggling some ideas around and 
writing some barebones, I stumbled upon a [gist with the same goal].  
Don't forget that it is a proof of concept, a test and good foundation for
better integration further in.

Finally we will have a fluent interface allowing for relative simple routing:

```javascript
var Routes = require('./Router/Router');
var app = new Routes();

app
  .get('/', function(req, res, next) {
      res.json({message: 'This is a get router.'});
  })
  .post('/', function(req, res, next) {
      res.json({message: 'This is a special post router.'});
  })
  .all('/', function(req, res, next) {
    res.json({message: 'You might try GET or POST.'});
  });
app.listen(8080);
console.log('Server started on Port 8080.');
```

[NodeJS does have]: <//www.npmjs.com/package/http-server>
[gist with the same goal]: <//gist.github.com/trevordixon/3061477>

## PhantomJS Rendering
As a headless browser one of its main advantages is rendering out images of 
webpages. Luckily, WebKit treats unset backgrounds already as transparent and
renders it on white backgrounds by default - so for a regular browser you'd
see a white background, however the rendered result is alpha-transparent. This
is great for our application - we get per default images with an intact alpha
channel.

The webpage documentation of PhantomJS lists three render functions: ``render,
renderBuffer, renderBase64`` After trying out [renderBuffer] I was
getting frustratingly no output. Since the ``renderBuffer`` function is called
inside a callback the resulting stacktrace gets eaten and never shows up.
Looking around the issue tracker of PhantomJS it became evident:
``renderBuffer`` is documented but not yet released. The workaround is pretty
simple: Instead of a Buffer we're using ``renderBase64`` and decode it back to a
buffer¹, set PhantomJSs' output encoding to binary and send the buffer¹ instead.  
Setting the appropiate headers for ``Content-Type`` and ``Content-Length`` makes
most software happy to accept images that way. 

¹ it's technically a string

{% highlight js %}
var Routes = require('./Router/Router');
var bodyParser = require("./API/BodyParser");
var app = new Routes();

app
  .use(bodyParser)
  .post(/.*/, function(req, res, next) { // _all_ post requests
    // required this way for parallel requests! :(
    var webPage = require('webpage'); 
    var page = webPage.create();
    // fill the dom: 
    page.content = req.body.html;
    var imageData = window.atob(page.renderBase64('PNG'));
    res.header('Content-Type', 'image/png');
    res.header('Content-Length', imageData.length);
    res.sendBinary(imageData);
  });
app.listen(8080);
console.log('Server started on Port 8080.');
{% endhighlight %}

Sending now a JSON like this in [Postman]:

![]({{site.url}}/images/2017-05-08-font-rendering-techniques/postman.png)

Please note that this right now read HTML and happily executes whatever is
inside there. You would totally be able to post malicious code into the page
context and as long as the rendering takes to process PhantomJS could do
anything within that context. You could in theory stall rendering indefinitly
and invoke remote code execution easily.  
**Again:** This API should never be able to be accessible from untrusted remote
sources.

[renderBuffer]: <//phantomjs.org/api/webpage/method/render-buffer.html>
[Postman]: <//getpostman.com/>

## Unity Integration

From here on out there is only little work left to do:

- Implement API in Unity
- Resolve the response as texture
- put the texture into a material

With the second code listing, the final Unity Code is really tiny:

{% highlight csharp %}
using System.Collections.Generic;
using UnityEngine;

public class PJSTextureLoader : MonoBehaviour {
    public Material mat;
    public string html;
    
    public IEnumerator HTMLLoader() {
        if(mat == null) {
            Debug.LogError("No material set");
            return null;
        }
        Dictionary<string, string> headers =
            new Dictionary<string, string>();
        headers.Add("Content-Type", "application/json");
        string requestBody = JsonUtility.ToJson(this);
        var www = new WWW(
            "localhost:8080",
            Encoding.UTF8.GetBytes(requestBody),
            headers
        );
        yield return www;
        mat.mainTexture = www.texture;
    }

    public void LoadTexture() {
        if(Application.isPlaying) {
            StartCoroutine(HTMLLoader());
        } else {
            Debug.LogWarning(
                "Cannot start coroutines in Edit Mode"
            );
        }
    }
}
{% endhighlight %}
That's all. The PNG already has transparency and with a transparent shader (or
a raw image / image GUI element) you should be able to see results immediatly.
My current implementation ships with a custom editor and a few more fields for
easier content binding. Since the renderer expects a html field in the json and
the TextureLoader class has a public html field, there will be a resulting field
present in the JSON. Otherwise you would either use a seperate struct for JSON
serialization or a library like [Newtonsofts' Json.NET]

[Newtonsofts' Json.NET]: <//www.newtonsoft.com/json>


# Edge cases, take aways and notes

## 1. Fixed width rendering
While testing out a few URLs to render, I noticed that PhantomJS sometimes has
trouble clamping the width - which makes good sense in many scenarios, since 
most websites care more about their presentation than their visual
scalability. On the other side, PhantomJS tries to render out either everything
or whatever is inside the ``clipRect``. The ``clipRect`` however needs fixed
``width`` and ``height`` - so it was, at first, not possible to fix the width
and render at a variable height based on the length of the visual content.  
This can be solved by letting the page scroll and hide the overflow:

{% highlight js %}
// assuming 'page' is from webpage.create()
page.evaluate(function(width) {
  var htmlElement = document.getElementsByTagName("html")[0];
  htmlElement.style.overflow = "hidden";
  htmlElement.style.width = width;
  htmlElement.style.maxWidth = width;

  document.body.style.overflow = "hidden";
  document.body.style.width = width;
  document.body.style.maxWidth = width;
}, width + "px");
{% endhighlight %}

## 2. Custom Fonts and local files
There are two ways loading html content: One with an URI context and one
without. The latter is by either typing an URL or write a ``file://`` URI. With
that you're allowed to reference files locally from CSS, HTML and scripts.
However, if you're dynamically set ``content`` on a ``webpage`` in PhantomJS, 
the URI context is missing and you will not be allowed to reference relative
files. In common browsers you're even disallowed to reference local files with
an absolute paths.  
While building the first drafts for rendering title textures, I was faced with
the problem of building it with custom fonts from our contractor. Chrome
prohibits local file access from a page - even if its served from a local file.
One variant is filling up CSS with a base64 string containing all the data of
that font. This, however, produces unreadable CSS and even more problematic
debuggability in case something goes wrong. While tinkering around, I tried
following snippet:

{% highlight css %}
@font-face {
    font-family: SpecialFont-ExtraBold;
    src: url(file:///<path>/Head/HeadWeb-ExtraBold.woff2);
}
{% endhighlight %}
This works right out of the box, while unexpected this allows for binding data
served from the local filesystem where PhantomJS is running. Additionally, you
can append to ``page.content`` css with all font faces and their styles, which
then clients can use. All without installing the font on the machine.

__Note__: This does not apply for loading a page from a file, like in the
example repo. Since you have a bound context with an URI, you're able to
reference files relative to that path.

## 3. A (last) word on security
I cannot stress this enough, but my code is nowhere secure. Lock up the machine,
bundle and host that rendering with the application that is using it. Or at
least,  lock up your network to disallow any communication on the port running
it. A simple [Slowloris] attack will lock up any outside communication of
PhantomJS, concurrent connection limit is set to 10 - everything else will be
"enqueued", which very likely will cause a memory issue when you SYN/ACK spam.
(However, I have yet to test this)

My example code gives everyone some free cpu time and a complete, well explored
eco system. If you're still allowing remote, outside machines interacting
with it, remember a few ceveats:
- sanitize input. Don't let anyone modify the DOM like that.
- allow binding blocks - i. e. CSS that you sanitize and / or singular divs.
- ``setTimeout`` is a good way to ``delete`` a webpage after a certain amount of
  time
- you have no access to the remote address of the system that is interacting
  with PhantomJS
- adding to that: PhantomJSs' stable build is old by now. Any issues introduced
  by [Mongoose] since then are shipped with the PhantomJS bundle.

## 4. Addendum
The code shown here is far inferior to the code on the repository, as it is
demonstration code.  
You will find a mysterious ``setTimeout`` inside the ``templateRender``
functions. These are caused by the time taken in which the DOM recalculates the
box sizing for all elements. There would be a more reliable way by subscribing
the the DOM Events, but I have yet to implement it. As of May 2017, this hasn't
been done.

# Code, Credits, Acknowledgements

The code can be seen on the [github repo] - it's licensed as MIT.  
@TrevorDixons' initial work was the fundamental initial step in writing an
express-like router for PhantomJS.  
Thanks to the PhantomJS maintainer - even if they've grown to a handful people
left. (Also the resilient ``#phantomjs`` Channel on ``ìrc.freenode.org``)

[Mongoose]: <//github.com/cesanta/mongoose>
[github repo]: <//github.com/artcom/textrender-service>


