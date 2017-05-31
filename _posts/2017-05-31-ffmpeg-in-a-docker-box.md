---
layout: post
title: FFmpeg in a Docker Box
author: <a href="https://github.com/crux/">Dirk Lüsebrink</a>
excerpt_separator: <!--end-of-excerpt-->
---
Ok, ok, it' called *container* actually, but, nonetheless, all of [FFmpeg] and its [compilation complexities] gets neatly abstracted away inside a docker _box_. My former boss teached me: _"It's always about how to get them into the box."_ My prefered tool being docker, then, if the only tool you have is docker, let the whole world look like a container.  

![http://ambitiondaily.com/whats-toolbox/]({{site.url}}/images/hammer.png)

[hammer image]: http://ambitiondaily.com/whats-toolbox/

<!--end-of-excerpt-->

[FFmpeg] is a blesssing and a course at the same time. To each and every "Can FFmpeg...?"  question concering video files and feeds of any kind the answer most probably is a resounding: **"YES!"**.

There are only two little problems: 

 1. How do I get it running on **my system** ?
 2. What combination of the gazillion params and options is need for **my task**?

I can't help you with the second question, but for the first one the nice people at [coconut.co] simply caged [FFmpeg] inside a docker container. Brilliant, gone the hassle!

Their container can be found as [opencoconut/ffmpeg] and they also wrote an article on some of the more tricky ways of using it: [Using FFmpeg with Docker], including for example this goodie:

```sh
$ alias ffmpeg='docker run -v=`pwd`:/tmp/ffmpeg opencoconut/ffmpeg'

Now FFmpeg can be used like it’s installed on your computer
```

## Why bother now?

This would be all fine and good, so why cloning it? I needed an updated version of ffmpeg for a command line option which was not available in the 3.0.2 of the original one. And, of course, to spread the word on how beautifully and simple docker removes complexity from plain sight and hides it behind version controlled Dockerfiles which carry the whole knowledge of what and how to put things together for the greater good. 

The greater good in this case is a research project of ours where we needed a transcoding micro service to bring live drone footage into HLS streaming format for consumptions on mobiles, tablets and possibly AR/VR, Hololens and Vive (buzzword bingo full score here). But this is for another post.

The updated [FFmpeg] version can be found within our Dockerfile in the [artcom/ffmpeg] repo. 

## Conclusion

Docker became one of my favorite tools real fast. FFmpeg, the beast, seems tamed for the first time I'm struggling with it, and I'm doing it already for a looong time.

[FFmpeg]: https://ffmpeg.org/
[compilation complexities]: https://trac.ffmpeg.org/wiki/CompilationGuide
[opencoconut/ffmpeg]: https://github.com/opencoconut/ffmpeg
[coconut.co]: http://coconut.co/
[Using FFmpeg with Docker]: https://medium.com/coconut-stories/using-ffmpeg-with-docker-94523547f35c
[artcom/ffmpeg]: https://github.com/artcom/ffmpeg

