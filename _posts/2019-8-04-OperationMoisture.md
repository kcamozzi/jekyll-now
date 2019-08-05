---
layout: post
title: Operation Moisture
---

![Operation Moisture](/images/moisture.png){: .align-center}

As a freshly graduted computer science student, I needed to find a project to keep myself occupied during my ongoing job hunt. I have recently taken an interest in automated gardening. Personally, I have no experience in gardening or growing plants in any capacity. My goal is to apply my skillset of electronics and technologies in order to build a sort of automated environment.
<!--more-->
Currently, my starting foundation is going to be very minimal. I have a tendancy to want to bite off more than I can chew at the beginning of projects so I decided to start small. Day 1 of my project consisted of the following:

* Setting up a linux environment on a raspberry pi zero w (Raspian)
* Wiring a DHT11 humidity/temperature sensor with additional moisture sensor
* Obtaining a basic planter box and flower seeds

And a list of all my current components
* Raspberry pi zero w (Pre-installed headers)
* Raspberry pi USB AC power adapter
* Mini HDMI adapter and Micro USB to USB 2.0 adapter for initial setup 
* Micro SD formatted with Raspian linux
* Numerous female to male jumper wires
* Breadboard
* Humidity, temperature and moisture sensors
* Wide planter box, potting soil and seeds

For the brains of the operation, I chose a [raspberry pi zero w](https://www.raspberrypi.org/products/raspberry-pi-zero-w/). Mine came preinstalled with male headers. I preferred this since I dont have much experience soldering, but you can buy them headless as well. I'm not going to need much processing power, and I love the compact size of the zero so this fit my project perfectly.
![Raspberry Pi Zero](/images/operationMoisture/rpi_hand.png){: .align-center}

The sensor I used is a [DHT11](https://www.osepp.com/electronic-modules/sensor-modules/71-humidity-moisture-breakout) humidity and temperature sensor with an additional moisture sensor for detecting if my soil is wet or dry. Honestly the temperature and humidity are additional sensors I didn't need at the beginning but it's nice to have them.

![Moisture Sensor](/images/moistureSensor.jpg){: .align-center}

After aquiring all my components, my first task was to make sure my pi was accessable by SSH so I wouldn't need to bother with additional peripherals. I also needed to be sure that the area in which I was placing my garden would be accessable by wifi. 

Now came the intimidating part, at least for me. Everything I was taught in school was purely software and code. Wiring and schematics aren't exactly my strongsuit. I followed the wiring schematic found on [circuitbasics.com's](http://www.circuitbasics.com/how-to-set-up-the-dht11-humidity-sensor-on-the-raspberry-pi/) tutorial. I followed the inital tutorial for the 4 pinned DHT11, but I noticed that their sensor wasn't coupled with a moisture sensor and they had a pin that was not used. After trial and error with my sensors, I figured out that the extra pin on my sensor was for the moisture sensor so I needed to wire that to a GPIO pin as well. I was using a raspberry pi zero so I used a [different pin layout](https://www.raspberrypi-spy.co.uk/wp-content/uploads/2012/06/Raspberry-Pi-GPIO-Layout-Model-B-Plus-rotated-2700x900.png) than the provided tutorial.

After I figured out the wiring, now was the time to test run some code. The circuitbasic's tutorial had suggested using Adafruit's DHT11 python library, so I cloned and installed their library. This allowed me to print the current temperature and humidity to the terminal. The most important function I wanted to get working today was the ability to check if the seeds needed watering. I used [piddlerintheroot's instructable](https://www.instructables.com/id/Soil-Moisture-Sensor-Raspberry-Pi/) on wiring and coding the moisture sensor. Although I did run into some issues. The event_detect and event_callback were not giving me the results from my sensor correctly. It seemed everytime I tested my sensor in a cup of water, it would trigger the "no water detected" string from the provided if statement. This would trigger when the sensor was dipped in the water and when it was removed. Very rarely would it read "water detected", but it did occur sometimes. I decided to output the value of the sensor using an infinite loop.

{% highlight python %}
while True:
        print(str(GPIO.input(channel)))
        time.sleep(1)
{% endhighlight %}

This allowed me to see everytime the sensor was "wet" I was getting a value of 1, and when the sensor was "dry" the value was 0. With this information, I was then able to combine portions of code from the Adafruit library and from piddlerintheroot's instructable to make a simple python script to output temperature, humidity and if the soil had moisture.

![Terminal output](/images/operationMoisture/terminal.png){: .align-center}

{% highlight python %}
#temp/humid gpio pin 15, moisture gpio pin 14

import sys
import Adafruit_DHT
import RPi.GPIO as gpio
import time

#type of sensor
sensor = 11
#humidity/temp gpio pin
htPin = 15
#moisture gpio pin
mPin = 14

gpio.setmode(gpio.BCM)
gpio.setup(mPin, gpio.IN)

humidity, temperature = Adafruit_DHT.read_retry(sensor, htPin)

temperature = temperature * 9/5.0 + 32

if humidity is not None and temperature is not None:
    print('Temperature = {0:0.1f} F* \nHumidity    = {1:0.1f}%'.format(temperature, humidity))
else:
    print('Failed to get reading. Try again!')

if gpio.input(mPin):
    print("Soil is moist")

else:
    print("Soil is dry, ready for moisture")
    sys.exit(1)
{% endhighlight %}

Here are some pictures of the first stage of this project.

![Planter overall view](/images/operationMoisture/small_IMG_20190805_151924.jpg){: .align-center}

![Sensor close view](/images/operationMoisture/small_MVIMG_20190805_151929.jpg){: .align-center}

![Components attached to planter](/images/operationMoisture/small_MVIMG_20190805_151938.jpg){: .align-center}


The planter is now neatly setup on my porch and hopefully I can continue to improve on this project. I've already brainstormed a few options for added features.
* Timelapse grow camera
* Scheduled watering updates via email or SMS
* Automated irrigation system
* internal/external web interface for monitoring plant health and growth

UPDATE: Woke up this morning to find that my command strips didn't hold very well on the side of the planter, and a little creature decided to take a nap on the adhesive. Don't worry, I was able to wet the strip and slowly peel him off and he was able to return back to his home! Silly little creature.

{: .center-image }![LIZARD!!](/images/operationMoisture/small_IMG_20190805_150559.jpg){: .align-center}

Stay tuned!