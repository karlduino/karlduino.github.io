---
title: 'co2 monitor that logs data to google sheets'
author: Karlduino
date: '2022-09-26'
slug: 'co2-logged-to-google-sheets'
categories: [projects]
tags: [wifi,eduroam,google forms,google sheets,data logger,covid]
---

In my [last
post](https://karlduino.org/2022/09/23/wifi-eduroam-google-forms/), I
went through the details of the (surprisingly easy) process of getting the enterprise wifi
[eduroam](https://eduroam.org) to work on an [ESP32
board](https://synthiam.com/Support/Hardware/Esp32-DevKit-v1), as well
as logging data to a web-based google spreadsheet using a google form.

I've now adapted my [CO<sub>2</sub>
monitors](https://karlduino.org/2022/09/22/co2-monitors/) to
automatically log their data to google sheets. I can swap out the
[Arduino Nano
Every](https://store.arduino.cc/products/arduino-nano-every) for an
[ESP32 board](https://synthiam.com/Support/Hardware/Esp32-DevKit-v1)
that costs half as much ($5 rather than $10), and everything [just
worked](https://karlduino/CO2monitorWifi).

![Looping video of six CO2 monitors with blinking blue lights, automatically posting data to a google spreadsheet](https://karlduino.org/images/wifi_co2_monitors.gif)

I pulled out the arduino, drilled a few more holes in the box,
and mounted the ESP32. (The mounting is nicer, since I can use the
larger 4/40 screws rather than those tiny 0/80 ones, and I can orient
the screws so that the rounded head is on the outside.)

I've got them set up to log the CO<sub>2</sub> measurements once per minute.
I also log the maximum and the average, though those aren't really
needed. The display is the same, and they continue to provide the same
information to the user, whether or not the wifi connection is
available. It's convenient that the university has
[eduroam](https://eduroam.org) all over campus, as the wifi connection
and the data logging should be able to occur anywhere.

The code is on [github](https://github.com/karlduino/CO2monitorWifi),
along with [assembly
instructions](https://karlduino.org/CO2monitorWifi/docs/instructions.html).
