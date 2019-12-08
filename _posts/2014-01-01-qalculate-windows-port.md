---
layout: blogpost
title: "New year, new platform: I ported Qalculate to Windows"
---

Update: my patches have been merged upstream. Get the Windows build [from the
qalculate downloads page](https://qalculate.github.io/downloads.html) directly.

<details><summary>Archived post for posterity</summary><div markdown="block">

Happy new year! I have a late Christmas gift to share with you...

[Qalculate](http://qalculate.sourceforge.net) is without doubt the best free calculator software for Linux. It has everything:

* arbitrary precision means you can calculate all the digits of pi you ever wanted to;
* a huge number of functions, neatly catalogued and available for your perusal on a rainy day;
* incredibly smooth built-in support for units, which makes dealing with physics and engineering problems a cinch;
* support for working with unknowns, which lets you factorise pesky quadratics with ease;
* conversion between bases (so you can see for yourself that Hallowe'en and Christmas are the same day really);
* there's even a periodic table in there, because clearly no calculator is complete without one;
* the fact that it's free, open source software is the delicious icing on the cake.

And now it's the best calculator software for Windows too! I've been working on porting it, and have finally got something that runs pretty decently.

<div style="text-align: center; font-size: 200%;">
<a href="https://github.com/chengsun/qalculate-gtk/releases/download/win32-140101/qalculate-win32-140101.zip">
Get it here!</a>
</div>
<p style="font-size: 75%; text-align: center;">(32-bit, requires Windows XP or above)</p>

Once unzipped to a suitable location, run `qalculate-win32-140101/bin/qalculate-gtk.exe`.

Browse my GitHub forks: [libqalculate](http://github.com/chengsun/libqalculate), [qalculate-gtk](http://github.com/chengsun/qalculate-gtk)

Things that don't work yet:

* Persistent state between sessions (no history, no saved custom definitions)
* Fetching currency exchange rates from the internet
* Graph plotting
* Help
* Possibly other stuff? (file a bug report in the GitHub repos linked above please!)

</div></details>
