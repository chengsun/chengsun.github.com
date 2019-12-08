---
layout: blogpost
title: "An unusual LED flasher, using only transistors"
lazyload: yes
---

<!-- TODO: video, pictures, schematics -->

The LED flasher is a popular electronics project often built by hobbyists and
students as an introduction to the basics of the field. The objective is to make
a light blink on and off at a regular rate, like a car indicator or bike light
would.

Looking around the internet reveals a remarkable number of different ways of
achieving the same effect. Some make use of [discrete components
only](https://en.wikipedia.org/wiki/Multivibrator), others using an oscillator
chip such as the popular [555 timer
IC](https://en.wikipedia.org/wiki/555_timer_IC). However two components are
always found: a resistor and capacitor are used to keep time and [determine the
frequency of the oscillation](https://en.wikipedia.org/wiki/RC_time_constant).

Here is my humble contribution to the collective catalogue of LED flasher
circuits. Its distinguishing feature is that it uses 6 transistors and **no
other components**.

In order to figure out how it works, let's rederive the design from scratch.

# A basic oscillator using ideal components

<details><summary>Circuit simulation</summary>

<iframe class="lazy" style="width: 100%; height: 20em;" data-src="http://www.falstad.com/circuit/circuitjs.html?cct=$+1+0.000005+16.817414165184545+50+5+50%0A159+320+240+320+336+0+1+100000000000000%0Ac+368+240+368+336+0+1e-10+2.1918047520401607%0Aw+320+240+368+240+0%0Aw+368+336+320+336+0%0Ag+368+336+368+352+0%0AR+368+176+368+128+0+0+40+9+0+0+0.5%0Aw+320+240+288+240+0%0Aw+288+288+304+288+0%0Ar+368+176+368+240+0+1000000000%0AO+368+240+432+240+0%0Aw+288+288+288+240+0%0Ao+9+64+0+4098+5+0.1+0+1%0A"></iframe>

<p markdown="span">

[Click for full screen version](http://www.falstad.com/circuit/circuitjs.html?cct=$+1+0.000005+16.817414165184545+50+5+50%0A159+320+240+320+336+0+1+100000000000000%0Ac+368+240+368+336+0+1e-10+2.1918047520401607%0Aw+320+240+368+240+0%0Aw+368+336+320+336+0%0Ag+368+336+368+352+0%0AR+368+176+368+128+0+0+40+9+0+0+0.5%0Aw+320+240+288+240+0%0Aw+288+288+304+288+0%0Ar+368+176+368+240+0+1000000000%0AO+368+240+432+240+0%0Aw+288+288+288+240+0%0Ao+9+64+0+4098+5+0.1+0+1%0A)

</p>
</details>

This circuit is made up of "ideal" components, which are easy to think about,
but don't exist in the real world. It also has a capacitor in it, which for our
intents and purposes also cannot be used. It's time to apply some ingenuity and
come up with a transistor-based substitute for each.

# Replacing the capacitor

MOSFET gate capacitance

# Replacing the ideal amplifier

<!-- TODO: make sure that somewhere in the above formulation there is actually an
amplifier -->

Common-emitter inverting amplifier

Stack two in a row. Pretty standard.

# Replacing the analogue switch

MOSFET as a switch. Pretty standard.

# Replacing the current source

<!-- TODO: make the above a current source -->

Bipolar transistor reverse bias leakage current

# Adding the LED

Leakage current too low

JFET as current limiter
