---
draft: true
date: 2024-06-24
authors:
  - me
categories:
  - Radio
---

# Tracking Flights with ADS-B
Radio is cool. One day, I'll get my HAM license and actually talk to other people using Radio... but for now, I'm having a lot of fun just listening. This post covers how I setup an ADS-B feeder for FlightAware.

## What is ADS-B?
Automatic Dependent Surveillance-Broadcast (ADS-B) is a technology used for tracking aircraft. Most aircraft are required to transmit their telemetry, which we can receive, and plot on a map. This is the primary source of data for sites like [FlightRadar](https://www.flightradar24.com/), [FlightAware](https://www.flightaware.com/live/map), and [ADS-B Exchange](https://globe.adsbexchange.com/). You may register as a "Feeder" for these sites, and in exchange receive a premium subscriptions to their tools, and broader datasets. As a feeder for FlightAware, you get an [Enterprise](https://www.flightaware.com/commercial/premium/#subscriptions) subscription for free.  

Many of these sites offer out of box kits for purchase: [ADS-B Turnkey Feeder Bundle](https://store.adsbexchange.com/collections/frontpage/products/deluxe-dual-sdr-feeder-kit). Though I prefer, and opted, to build my own.

## The Hardware
Many of the items on this list are excessive. You can get by with simply: a Pi, an RTL-SDR dongle, and a cheap antenna. I added a 2nd receiver so that I could listen in the 978 MHz range associated with the UAT frequency. This is a cheaper alternative to 1090 MHz for use by general aviation craft. 

!!! note

    The __vast__ majority of aircraft are using 1090 MHz. Of all the aircraft I've tracked, 6% of them have been transmitting over 978 MHz.

My Hardware:

 - [Short USB Extensions](https://a.co/d/04jZBreV)
 - [SMA Splitter](https://a.co/d/0cGnkEcm)
 - [2x RTL-SDR v3](https://a.co/d/0d5nYy7S)
 - [Raspberry Pi 4B](https://www.microcenter.com/product/637834/raspberry-pi-4-model-b-4gb-ddr4)
 - [PoE Hat](https://a.co/d/0flXcGep)
 - [SMA Adapter](https://www.dxengineering.com/parts/cma-hm-05jsj)
 - [Discone Antenna MFJ-1868](https://www.dxengineering.com/parts/mfj-1866)

### RTL-SDR
These dongles are wildly popular, inexpensive receivers used Software Defined Radio. There's a newer v4 model, but don't buy it for ADS-B. I've found the ADS-B driver support for the v3 model is much better (for now). Beware of knock offs. There's a whole section of RTL-SDR site dedicated to help you spot fake dongles [here](https://www.rtl-sdr.com/buy-rtl-sdr-dvb-t-dongles/).

### Discone Antennas
Discones are great for a wide variety of applications. AM/FM radio, ADS-B, Weather, Air Traffic Control... Anything transmitting in the 10s of MHz range, up to low GHz, A discone will be able to receive. It's overkill for this specific application, but I plan to use this antenna for more than just tracking planes overhead.

### The Setup
Here's what it looks like all put together.

![The Setup](../../assets/posts/tracking-flights-overhead/the-setup.png)

The software installed on the Pi gives you a local endpoint to hit that shows _only_ flights overhead.

![flights-overhead](../../assets/posts/tracking-flights-overhead/flights-overhead-dark.png#only-dark)
![flights-overhead](../../assets/posts/tracking-flights-overhead/flights-overhead-light.png#only-light)

## The Software

 - [RTL-SDR](https://github.com/osmocom/rtl-sdr)
 - [libusb](https://libusb.info/)
 - [RaspberryPi OS](https://www.raspberrypi.com/software/)






51 out of 738


  
    
      

eeprom set serial number: https://discussions.flightaware.com/t/how-to-serialize-dongles-for-es1090-uat978/48147
Configure UAT 978: https://discussions.flightaware.com/t/easiest-way-to-add-978/59106/2
Piaware config settings in /etc/piaware.conf: https://www.flightaware.com/adsb/piaware/advanced_configuration



[^1]: In the US, aircraft may transmit in 