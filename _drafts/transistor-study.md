---
layout: blogpost
title: "An unusual LED flasher, using only transistors"
lazyload: yes
style: |
  #pic-table .label {
    padding-right: 0.4em;
    text-align: right;
  }
  #pic-table img {
    width: 20em; 
    max-width: 100%;
  }
---

<video autoplay loop 
       style="float: right; padding: 0.5em; width: 20em; max-width: 50%;"
       src="/assets/images/transistor-study.webm" 
       type="video/webm">
My LED flasher in action. Sorry, your browser doesn't support embedded videos.
</video>

The LED flasher is a popular electronis project built by hobbyists and students
which demonstrates some fundamental principles of electronics.

A brief look around the internet reveals [a remarkable number of different ways
of achieving the
effect](https://www.youtube.com/results?search_query=led+flasher+diy). Some make
use of [discrete components only](https://en.wikipedia.org/wiki/Multivibrator),
others use an oscillator chip such as the popular [555 timer
IC](https://en.wikipedia.org/wiki/555_timer_IC). Two components can be found in
practically all[^all] designs: a resistor and capacitor, used to keep time and
[set the frequency of
oscillation](https://en.wikipedia.org/wiki/RC_time_constant).

[^all]: 
    There do of course exist ways of making an LED flasher whilst avoiding using
    any discrete resistor or capacitor. For instance, we can cheat and delegate 
    the task of making an oscillator to someone else:

    - Using a microcontroller with an internal on-chip oscillator;
    - Using a special LED with in-built flashing circuitry.
    
    Or, more interestingly, we can leave the realm of pure electronics. For 
    example:
    
    - Detecting a motor's period of revolution using a permanent magnet and a 
      [Hall effect sensor](https://en.wikipedia.org/wiki/Hall_effect_sensor)
    
    For more wild and wacky ideas, check out [The Flashing Light 
    Prize](https://www.flashinglightprize.com/).

This page documents my small contribution to the collective catalogue of LED
flasher circuits. Its distinguishing feature is that aside from its +9V power
jack and LED[^led-colour], the entire circuit consists solely of 6 transistors.
There is *neither a resistor nor a capacitor present*.

[^led-colour]:
    I used a standard white LED. However, my circuit regulates LED current
    independently of forward voltage, so any colour can be swapped in without
    requiring adjustment to the component values.

<div style="text-align: center;">

<img src="/assets/images/transistor-study-main.jpg"
     alt="My LED flasher"
     style="width: 20em; max-width: 100%;" />

<table id="pic-table" style="display: inline-block;">
  <tr><td class="label" markdown="span">Front:</td><td>
    <img src="/assets/images/transistor-study-front.jpg"
        alt="My LED flasher (front)" />
  </td></tr>
  <tr><td class="label" markdown="span">Back[^date]:</td><td>
    <img src="/assets/images/transistor-study-back.jpg"
        alt="My LED flasher (back)" />
  </td></tr>
  <tr><td class="label" markdown="span">Layout:</td><td>
    <img src="/assets/images/transistor-study-pcb.png"
        alt="My LED flasher (PCB)" />
  </td></tr>
</table>

</div>

[^date]:
    You may notice that I designed this circuit quite some time ago: the back of
    the PCB has 2018-07-01 printed on it. Indeed, it wasn't until now that I had
    a chance to go back to this little project and finish the write-up.
    
    There's some other neat stuff in the works right now, but I'm not sure when 
    those will end up getting written up, sorry. Consider myself a very deeply 
    pipelined processor!

In order to figure out how it works, let's rederive the design from scratch.

# The base oscillator

I wanted to start with a simple oscillator design that's shown to work, without
restricting myself to any component limitations; I would then attempt to replace
the pieces in that design one by one with functionally-equivalent
transistor-only alternatives.

The latter step is certainly not trivial; sometimes it may not even be possible.
In electronics, each individual type of component has a number of special
properties. This one component might be used for any of a number of purposes
depending on context; however not all purposes require all of the special
properties of that component. In order to be able to substitute a component with
an alternative of our choosing, we don't have to match all of the special
properties of the former; only the subset of the properties that are needed to
achieve the original intended purpose.

To give an example: the humble capacitor has the following special properties:

- when charging, the voltage across it increases linearly;
- when charging, energy is stored up that can be released later.

Now let's consider a circuit such as the [capacitance
multiplier](https://en.wikipedia.org/wiki/Capacitance_multiplier), which
simulates a large capacitor in voltage, but not in energy. When is it OK to
substitute a real capacitor in a circuit with a capacitance multiplier?

- If the capacitor's purpose is to form an RC timing circuit, only the former
property is needed, so this is fine. 
- However, if the capacitor's purpose is to store up energy (e.g. for a camera
flash), we would not be able to make the substitution.


The usual approach to creating discrete oscillators is to use a
[multivibrator](https://en.wikipedia.org/wiki/Multivibrator), two
cross-connected transistors that are locked in a mutual positive feedback loop.
So this is what I started with as a known quantity. 
I played around with this design a little bit but found the circuit difficult
to modify to my needs. Each component was serving multiple purposes, and the
interactions between each component felt quite tightly intertwined with each
other.

This was bad news for me because my transistor-only alternatives would have to recreate a large number of these 
and the capacitors in particular seemed to be hard to replace
with substitutes.

Unhappy with the number of support components required (the circuit requires two
of everything), and the concern about whether I would be able to 

I decided to come up with something simpler myself.

I drafted together a concept in my head based on a resetting integrator.

The general principle is to have there is a constant

<details><summary>Click to show circuit and simulator</summary>

<iframe class="lazy" style="width: 100%; height: 20em;" data-src="http://www.falstad.com/circuit/circuitjs.html?cct=$+1+0.000005+16.817414165184545+50+5+50%0A159+320+240+320+336+0+1+100000000000000%0Ac+368+240+368+336+0+1e-10+2.1918047520401607%0Aw+320+240+368+240+0%0Aw+368+336+320+336+0%0Ag+368+336+368+352+0%0AR+368+176+368+128+0+0+40+9+0+0+0.5%0Aw+320+240+288+240+0%0Aw+288+288+304+288+0%0Ar+368+176+368+240+0+1000000000%0AO+368+240+432+240+0%0Aw+288+288+288+240+0%0Ao+9+64+0+4098+5+0.1+0+1%0A"></iframe>

<p markdown="span">

[Click for full screen version](http://www.falstad.com/circuit/circuitjs.html?cct=$+1+0.000005+16.817414165184545+50+5+50%0A159+320+240+320+336+0+1+100000000000000%0Ac+368+240+368+336+0+1e-10+2.1918047520401607%0Aw+320+240+368+240+0%0Aw+368+336+320+336+0%0Ag+368+336+368+352+0%0AR+368+176+368+128+0+0+40+9+0+0+0.5%0Aw+320+240+288+240+0%0Aw+288+288+304+288+0%0Ar+368+176+368+240+0+1000000000%0AO+368+240+432+240+0%0Aw+288+288+288+240+0%0Ao+9+64+0+4098+5+0.1+0+1%0A)

</p>
</details>

This circuit is made up of "ideal" components, which are easy to think about,
but don't exist in the real world. It also has a resistor and capacitor in it,
which for our intents and purposes also cannot be used. It's time to apply some
ingenuity and come up with a transistor-based substitute for each of these
problem components.

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
