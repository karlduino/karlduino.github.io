---
title: 'co2 monitors'
author: Karlduino
date: '2022-09-22'
slug: projects-page
categories: [news]
tags: [news,covid]
---

My [latest project](https://github.com/karlduino/CO2monitor) is a
low-cost carbon dioxide monitor.

[![CO2 monitor tied to a wooden post with a pink cord](https://karlduino.org/CO2monitor/docs/pics/co2monitor_sm.jpg)](https://karlduino.org/CO2monitor/docs/pics/co2monitor.jpg)

Many workers at UW-Madison have been concerned about air quality on
campus. The university has not been forthcoming about what effort they
have devoted to assessing and improving air quality in campus
classrooms, meeting rooms, and workspaces. (UW-Madison Facilities has
an [FAQ on
HVAC](https://facilities.fpm.wisc.edu/facility-manager-tools/#faq-hvac)
that is shockingly offensive, even for a completely jaded employee.)
And with the removal of the
campus mask mandate at spring break last March, mask-wearing on
campus has become a rarity.

And so we've started an effort to help people assess air quality in
their classrooms and workspaces, beginning with measurement of CO<sub>2</sub>,
which can be a good proxy to indicate air ventilation. If
CO<sub>2</sub> levels are >1200 ppm, that would indicate that the
space is not getting adequate fresh air. One could then try to open
doors and windows to improve ventilation, or if that is not possible,
could add an air filter such as a hand-made [Corsi-Rosenthal
box](https://cleanaircrew.org/box-fan-filters/).

The main commercial CO<sub>2</sub> monitor is the
[Aranet4](https://aranet.com/products/aranet4/), which is really a
beautiful device: an e-ink display, bluetooth to connect to a
phone app, accurate measurements, and runs for weeks and weeks on two
AA batteries. But it costs **$250**!

There are dozens of lower-cost alternatives, from $20-60, but none of
them seem to work well at all.

And so, inspired by
[LibreCO2](https://github.com/danielbernalb/LibreCO2), I put together
the simplest possible device using a [SenseAir S8 low power
sensor](https://senseair.com/products/size-counts/s8-lp/), an [Arduino
Nano Every](https://docs.arduino.cc/hardware/nano-every), and a 16x2
LCD display (with an I2C chip). The cost is about $40 for the sensor,
$10 for the arduino, and $5 for the LCD display, so $65. Plus a
clear plastic box for 50 cents. If you order the parts from [AliExpress](https://www.aliexpress.com/)
(with a one-month lead time, buying parts for 10, and using a generic
[Arduino Nano v3
clone](https://www.aliexpress.com/item/2255799923112796.html?spm=a2g0o.order_list.0.0.15e41802Y2Qmjn)),
you can get the price down to <$35 each.

The [github repository](https://github.com/karlduino/CO2monitor/) for
the project includes detailed [assembly
instructions](https://karlduino.org/CO2monitor/docs/instructions.html).

There were three main challenges that we dealt with.

First, the Arduino Nano Every was a bit of a pain, in trying to upload
a sketch. The Arudino IDE gives lots of error messages for these
microcontrollers, and tends to hang. In the end, it seems like you try
to upload a sketch once and get one error, and then you do it a second
time and get a different error, but the second time actually works
(though it is a pretty slow upload).

Second, I had a lot of difficulty mounting the Nano Every in the
plastic box. I would have prefered to have the screws on the outside
and the nuts on the inside, but there's not any room for the nuts next
to the pins, so I did it the other way around. And for many of them, I
was getting an apparent short-circuit between the screw and the
micro-USB, and so I generally left out one of the planned screws to
next to the USB.

Third, my collaborator on this bought [some LCD displays from
Digi-Key](https://www.digikey.com/en/products/detail/orient-display/AMC1602AR-B-B6WTDW-I2C/12089223)
that had I2C integrated, but with quite different pin-out from what
I'd been using. It was a bit of a struggle to figure out how to use
these, and we ended up [forking the code to a separate
version](https://github.com/karlduino/CO2monitor/tree/main/CO2monitor_altLCD)
for these.

It's exciting to have a few of these CO<sub>2</sub> monitors out on
campus, measuring air quality. We're getting some data back which is
in many cases good, but in at least one case very, very bad (the
dreaded [Humanities building](https://map.wisc.edu/s/5dryqhg7)).

I'm so excited that I'm now working on replacing the microcontroller
with one that includes wifi access, to simplify the data gathering
aspect, and to get more complete data. More to come on that.
