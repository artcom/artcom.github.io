---
layout: post
title: FFmpeg in a Docker Box
author: <a href="https://github.com/crux/">Dirk Lüsebrink</a>
excerpt_separator: <!--end-of-excerpt-->
---
You call the box *container* of course. Nonetheless, [FFmpeg] and its [compilation complexities] gets neatly abstracted away inside this container. My former boss taught me: _"It's always about how to get them into the box."_ My preferred tool being docker, then, if the only tool you have is docker, let the whole world look like a container.

![http://ambitiondaily.com/whats-toolbox/]({{site.url}}/images/hammer.png)

[hammer image]: http://ambitiondaily.com/whats-toolbox/

<!--end-of-excerpt-->

[FFmpeg] is a blessing and a curse at the same time. To each "Can FFmpeg do..?"  question concerning video files and feeds of any kind the answer most probably is a resounding: **"YES!"**.

There are only two little problems: 

 1. How do I get it running on **my system** ?
 2. Which combination of the gazillion params and options to I need for **my task**?

I can't help you with the second question. For the first one the nice people at [coconut.co] simply caged [FFmpeg] inside a docker container. Brilliant, gone the hassle!

Their container is called [opencoconut/ffmpeg]. They also wrote an article on some of the trickier options in: [Using FFmpeg with Docker]. You should check it out, see this one for example:

```sh
$ alias ffmpeg='docker run -v=`pwd`:/tmp/ffmpeg opencoconut/ffmpeg'

Now FFmpeg can be used like it’s installed on your computer
```

## Why bother now?

This would be all fine and good, so why cloning it? I needed an updated version of ffmpeg for a command line option which was not available in the 3.0.2 of the original one. And, of course, to spread the word on how simple docker removes complexity from plain sight and hides it behind version controlled Dockerfiles.  Dockerfiles carry the whole knowledge. They contain what you need and how to put it together for the greater good.

The greater good in this case is a research project of ours. We need a transcoding micro service to bring live drone footage into HLS streaming format for consumptions on mobiles, tablets and possibly AR/VR, Hololens and Vive (buzzword bingo full score here). But this is for another post.

You find our Dockerfile with the updated [FFmpeg] version in the [artcom/ffmpeg] repo.

## Conclusion

Docker became one of my favourite tools real fast. FFmpeg, the beast, seems tamed for the first time I'm struggling with it, and I'm doing it already for a looong time.

[FFmpeg]: https://ffmpeg.org/
[compilation complexities]: https://trac.ffmpeg.org/wiki/CompilationGuide
[opencoconut/ffmpeg]: https://github.com/opencoconut/ffmpeg
[coconut.co]: http://coconut.co/
[Using FFmpeg with Docker]: https://medium.com/coconut-stories/using-ffmpeg-with-docker-94523547f35c
[artcom/ffmpeg]: https://github.com/artcom/ffmpeg

