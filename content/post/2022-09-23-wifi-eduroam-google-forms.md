---
title: 'eduroam wifi and google forms with an ESP32'
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

How about using wifi? I had just bought a little [ESP32-S3 TFT
feather](https://www.adafruit.com/product/5483) (mostly because they
showed it in use with the [SCD-30 CO<sub>2</sub>
monitor](https://www.adafruit.com/product/4867), and it just seemed
really cool with the built-in high-resolution display). These ESP32
microcontrollers have built-in wifi (and bluetooth). And then I realized that if we logged data
to a google sheet via a google form, we wouldn't need to worry about
time, because google would time-stamp all of the entries for us.

Surprisingly, I could get this ESP32 up and running with the SCD-30
sensor, logging data to google sheets, and connected to wifi, and then
even connected to wifi at the university using
[eduroam](https://eduroam.org). It was just 4 hours work or so, and
there very few road blocks.

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
particularly simple. And combining the [example for the SCD-30 sensor](https://github.com/adafruit/Adafruit_SCD30/blob/master/examples/oled_co2_monitor/oled_co2_monitor.ino)
and the [example for the built-in TFT display](https://github.com/adafruit/Adafruit-ST7735-Library/blob/master/examples/graphicstest_feather_esp32s2_tft/graphicstest_feather_esp32s2_tft.ino),
it was up and running super-quick. But note that the [SCD-30
CO<sub>2</sub> sensor](https://www.adafruit.com/product/4867) is more expensive
than the [SenseAir S8
sensor](https://senseair.com/products/size-counts/s8-lp/) I've been
using, and it seems to be giving measurements that are too high.

### Google forms to gather data

We've been using a [google
form](https://docs.google.com/forms/d/e/1FAIpQLSdxHELs5_nhfD2l0LLOn0AP_aOi-45dspcFVoiOKbdar_uYsw/viewform)
to gather requests for our lending library of CO<sub>2</sub> monitors; the
results are collected in a google sheet. I'd not done this myself,
but it seemed like a great option for collecting data from sensors,
and so that's where I started.

[This
instructable](https://www.instructables.com/Post-to-Google-Docs-with-Arduino/)
spelled out the details quite well. They use
[pushingbox](https://www.pushingbox.com/) as an intermediary between
the arduino and the google form. (It's not entirely clear to me why;
maybe to turn an https request into an http one?) But the rest of it
was quite clear.

The basic steps are to set up a google form, grab the super-long
ID for the form, grab the weirdly-named key identifiers for the
form elements, and then working out the REST API request that we will use
to push data to the form.

First, go to [Google Docs](https://docs.google.com) and click on the
hamburger button (i.e., the button with the three lines) in the top-right, and click
_Forms_, then start a new blank form. Give it a name, create a first
question (as just the variable name you want in the column in the
final spreadsheet), select _Short answer_ in the drop-down menu, and
then click the plus sign in the circle on the far right to create
another field, or click the _Send_ button on the top-right when you're
done. You don't really need to send it to anyone, but grab the full
link to the form, which includes (after `forms/d/e`) the super-long
form ID that you'll need.

Next, open the form in Firefox and click Ctrl-Shift-I to open the
"Inspector". Then poke through the html to find the `<form>` element.
If you poke around inside there, you'll find the `<input>` elements.
You're looking for their names, as they have the field names that
google uses, and which you will use when you want to post data
programmatically. They'll look something like this:

```
<input type="hidden" name="entry.1240506587" value="">
<input type="hidden" name="entry.1745392992" value="">
```

You want to save those `entry.###` names.

You can now use the web browser to post data to the form, like this:

```
https://docs.google.com/forms/d/e/SUPER_LONG_FORM_ID/formResponse?submit=Submit&usp=pp_url&entry.1240506587=my_first_entry&entry.1745392992=my_second_entry
```

You replace the `SUPER_LONG_FORM_ID` with your actual super long form
identifier, and then the `entry.###` fields and the values you want
to post. This is what we'll use when we post data from the
microcontroller, though first we need to get it connected to wifi. So
we'll come back to this in a moment.

But before connecting to wifi, go to your form at
<https://forms.google.com> and you'll find three tabs: Questions,
Responses, and Settings. If you click Responses, there's a little
green box with a white cross in it; click that to create a
spreadsheet to collect your results.


### Wifi

Connecting this ESP32 microcontroller to my home wifi was super easy.
Adafruit has a straightforward [example on
github](https://github.com/adafruit/Adafruit_Learning_System_Guides/blob/main/ESP32_S2_WiFi_Tests/WiFiWebClient/WiFiWebClient.ino).

The basics are to define character strings with the SSID and password
for your wifi. I put these as defined values in a `private.h` file to
keep them out of the [code that I post to github](https://github.com/karlduino/co2_tft).

Use `#include <Wifi.h>`, and `Wifi.begin(ssid, password)` to connect,
with `WiFi.status()`, `WiFi.SSID()` and `WiFi.localIP()` to give you
information about how it's working. Also `WiFi.RSSI()` to get signal
strength.



### Pushing data to google

If you look at that [Adafruit Wifi
example](https://github.com/adafruit/Adafruit_Learning_System_Guides/blob/main/ESP32_S2_WiFi_Tests/WiFiWebClient/WiFiWebClient.ino),
it includes an example GET request.
That's basically what we want to use to push data to our google form.
But google wants an SSL connection (https rather than just http) which
adds one small difficulty: you need to grab google's
SSL certificate and include it within your code. (I followed [this
tutorial](https://techtutorialsx.com/2017/11/18/esp32-arduino-https-get-request/)
for the root CA certificate business.)

First, go to the google forms site in Firefox, <https://forms.google.com>,
and click on the little lock by the URL. Click _Connection secure_ and
then _More information_. Then under _Security_, click _View
certificate_. Click the tab that's like _GTS Root R1_ and then scroll
down to _Miscellaneous_ and click _PEM (cert)_ next to _Download_. This
saves a `.pem` file that is the text of the certificate. Paste that
into your sketch, adding a bunch of double-quotes and newlines and
crap, to create a character string containing the full certificate, as
[in my Arduino sketch for this
project](https://github.com/karlduino/co2_tft/blob/main/co2_tft.ino#L12-L34).

To push data to the form, you can follow the [WifiClientSecure
example](https://github.com/espressif/arduino-esp32/tree/master/libraries/WiFiClientSecure/examples/WiFiClientSecure)
with arduino-esp32 on github.

You use `WifiClientSecure client;` to define the object that will
be the connection to a web server. You use
`client.setCACert(root_ca);` to use the root certificate that you'd
downloaded. You build up a character string with the URL for the GET call to send data to the
google form, and then use `client.connect(api_host, httpPort)` to
connect to the google server, `client.print()` to dump your form data,
and `client.stop()` to close the connection.

Make sure to control how often data gets posted, say once a minute, or
once every two or five minutes. Mess this up and you can find yourself
posting data thousands of times per second and then getting banned by
google.

Once you've gotten things going, go to your google sheet for the
responses to your form and watch your data accrue.


### Eduroam

So the last thing, and the one that I expected to have the most
trouble with, was
connecting to Wifi at my university, which uses
[eduroam](https://eduroam.org). An advantage here is that, if I can
get the device to connect, it will be able to connect in any building
on campus. The disadvantage is that whenever I get a new linux laptop,
it seems a bit of a mess to get it to connect to eduroam.

Happily though, in this case it was surprisingly easy. I was able to
follow the [WifiClientEnterprise
example](https://github.com/espressif/arduino-esp32/blob/master/libraries/WiFi/examples/WiFiClientEnterprise/WiFiClientEnterprise.ino),
and I didn't need any sort of certificate file at UW-Madison, but
just the EAP_USERNAME and EAP_PASSWORD, which I again defined in the
`private.h` file not included in the [github repository](https://github.com/karlduino/co2_tft).

I was able to connect to wifi at work via eduroam just as
easily as I was able to do so on my home network.
But your company or university setup may be more complicated and
difficult. Consider [this
repository](https://github.com/martinius96/ESP32-eduroam), and also
[this one](https://github.com/debsahu/Esp32_EduWiFi), which also has
an accompanying [youtube video
tutorial](https://www.youtube.com/watch?v=bABHeMea-P0).

### Next steps

So I was able to show that I could measure CO<sub>2</sub> levels and use a wifi
connection and a google form to push the data to a google spreadsheet.
But the particular microcontroller and sensor I was using here are too
expensive to put into broad practice. Instead, I want to be able to
substitute a simple wifi-enabled microcontroller into the [small build
that I already have](https://karlduino.org/CO2monitor), and as cheaply
as possible.

Probably I'll end up going with a relatively generic [ESP32
microcontroller](https://en.wikipedia.org/wiki/ESP32); it seems like
you can get them for about $6 each if you buy a multi-pack. But I'm
also interested in the [Arduino Nano 33
IoT](https://store.arduino.cc/products/arduino-nano-33-iot) and the
[Arduino Nano RP2040
Connect](https://store.arduino.cc/products/arduino-nano-rp2040-connect).
They're more expensive but could more easily be fit into my existing
boxes. The [Raspberry Pi Pico
W](https://www.raspberrypi.com/news/raspberry-pi-pico-w-your-6-iot-platform/)
seems like it could also work, but it also seems like it'll be quite a
different toolchain for the software development aspects. I could
also go with the cheapest route, of an
[ESP8266](https://en.wikipedia.org/wiki/ESP8266), though the [tutorial
on eduroam at U Michigan](https://github.com/debsahu/Esp32_EduWiFi)
suggested that they could get eduroam working on the ESP32 but not on the
ESP8266.

If I go with the ESP32, the first steps will be connecting to my
SenseAir S8 sensor and LCD display. If I can get those to work, then
I'm confident that the rest will follow (connecting to wifi and pushing
data to the google form).
