---
title: 'Wifi, eduroam, and google forms with an ESP32'
author: Karlduino
date: '2022-09-23'
slug: wifi-eduroam-google-forms
categories: [projects]
tags: [wifi,eduroam,google forms,covid,arduino]
---

I was excited by our initial success at a [low-cost carbon dioxide
monitor](https://karlduino.org/2022/09/22/co2-monitors/), so much so
that I was immediately thinking about how to scale things up, and how
to get more data and in a less cumbersome way.

Our initial build just has a CO<sub>2</sub> sensor, a simple Arduino
microcontroller, and an LCD display. It shows the current
CO<sub>2</sub> concentration plus the maximum and average since it was
last turned on. To gather data, we need people to write down those
values (or take a picture) before they turn it off.

What could we do to gather more detailed data, without increasing the
cost of the device? For about $3, we could add a SD card logger. But
that would mean pulling out SD cards regularly. And wouldn't we need
to include a clock?

How about using Wifi? I had just bought a little [ESP32-S3 TFT
feather](https://www.adafruit.com/product/5483), mostly because they
showed it in use with the [SCD-30 CO2
monitor](https://www.adafruit.com/product/4867), and it just seemed
really cool with the built-in high-resolution display, and these ESP32
microcontrollers include Wifi. Then I realized that if we logged data
to a google sheet via a google form, we wouldn't need to worry about
time, because the entries would all be time-stamped.

Surprisingly, I could get this ESP32 up and running with the SCD-30
sensor, logging data to google sheets, and connected to Wifi, and then
even connected to Wifi at the university using
[eduroam](https://eduroam.org). It was just 4 hours work, or so, and
very few road blocks.

### ESP32 with SCD-30

The first thing was to get the ESP32-S3 TFT feather up and running and
connected to the SCD-30 sensor. I'm going to use the Arduino IDE to
program it.

This was straightforward using the [tutorials at
Adafruit](https://learn.adafruit.com). The [tutorial on ESP32-S3
TFT feather](https://learn.adafruit.com/adafruit-esp32-s3-tft-feather)
explains the basics, though actually
[the tutorial for the previous version,
ESP32-S2](https://learn.adafruit.com/adafruit-esp32-s3-tft-feather/using-with-arduino-ide),
is more complete and overall more helpful. Key steps include adding
an additional Boards Manager URL in the Arduino IDE preferences, and
then downloading a number of extra libraries,
including the
[Adafruit ST7735 and ST7789
library](https://www.arduino.cc/reference/en/libraries/adafruit-st7735-and-st7789-library/)
for the built-in TFT and the
[Adafruit SCD30
library](https://www.arduino.cc/reference/en/libraries/adafruit-scd30/)
for the sensor.

![ESP32-S3 TFT connected to SCD-30 sensor, sitting on a breadboard but not soldered](/images/adafruit_esp32-s3_tft.jpg)

I'd bought a [STEMMA QT cable](https://www.adafruit.com/product/4210)
which made the connection between the board and the sensor
particularly simple. But taking the [example for the SCD-30 sensor](https://github.com/adafruit/Adafruit_SCD30/blob/master/examples/oled_co2_monitor/oled_co2_monitor.ino)
plus the [example for the built-in TFT display](https://github.com/adafruit/Adafruit-ST7735-Library/blob/master/examples/graphicstest_feather_esp32s2_tft/graphicstest_feather_esp32s2_tft.ino) and putting them
together, it was up and running super-quick. But note that the [SCD-30
CO2 sensor](https://www.adafruit.com/product/4867) is more expensive
than the [SenseAir S8
sensor](https://senseair.com/products/size-counts/s8-lp/) I've been
using, and it seems to be giving measurements that are too high.

### Google forms to gather data



### Wifi



### Eduroam
