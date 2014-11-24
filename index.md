---
layout: default
title: Cheng Sun - Home
---

I am an 18-year-old student, currently studying Computer Science with Maths at Churchill College, University of Cambridge.

# Blog

{% for post in site.posts %}
* {{ post.date | date: "%Y-%m-%d" }} &mdash; [{{ post.title }}]({{ post.url }})

{% endfor %}

# JavaScript Hackery

Most of this is very old stuff that I made back in secondary school and sixth form in my free time.

* Revision question sets
    * [OCR AS Economics](economics.html) questions
    * [OCR AS Physics material vocab](physics.html) questions

* [ocreMutiny](ocremutiny.html) &mdash; a tiny 1K emulator for the OCR A2 Electronics assembly language

* Verlet integration experiments
    * [Ribbon](verletribbon.html) in low gravity
    * [Triangles](verlettri.html)
    * [Interactive ragdoll game](verletrag.html)
    * [Interactive bridge simulation](verletbridge.html) (unfinished)

* 3D experiments with 2D canvas
    * [Morphing points](points.html)
    * [Rotating 3D cube](cube.html)

* Fractals and automata
    * [Animated Julia set](julia.html). Uses randomised backwards iteration
    * [Tinkerbell map](tinkerbell.html). Beautiful order from chaos.
    * [Langton's ant](walk.html) I used this to visualise [a PE problem](http://projecteuler.net/problem=349). There is a bug when the ant hits the edge of the simulation; the glider pattern is meant to continue forever.
    * [Linear congruential generator](lcg.html) (LCG) patterns. Swirls and shapes emerging out of randomness.

* [Minesweeper](mine.html)

* [Snake in 1K](snake.html)

# Things I do

* I was part of [Systemetric](http://systemetric.org/), the Hills Road Sixth Form College robotics team. We build and code autonomous robots for the [Student Robotics](https://www.studentrobotics.org/) competition. Find us on [GitHub](https://github.com/Systemetric).

* I worked on the [VLC Mozilla browser plugins](https://github.com/chengsun/gtk-npapi-vlc) as part of [Google Code-In 2011](http://google-opensource.blogspot.co.uk/2012/02/google-code-in-2011-grand-prize-winners.html), of which I won the grand prize.

* I developed the [Firefox SPDY indicator](https://addons.mozilla.org/en-US/firefox/addon/spdy-indicator/), a Firefox addon that indicates when a web page was served through [SPDY](http://www.chromium.org/spdy).

* I do competitive algorithmic coding, and was part of the [British team](http://olympiad.org.uk/2011/index.html) in the [International Olympiad in Informatics 2011](http://www.ioi2011.or.th/) and [2014](http://www.ioi2014.org/).

* I enjoy taking photos. [Check them out!](http://www.flickr.com/photos/118277575@N06/)

* I am a user (and big fan) of [Arch Linux](https://www.archlinux.org/).

[![Project Euler](http://projecteuler.net/profile/infinigon.png)](http://projecteuler.net)
