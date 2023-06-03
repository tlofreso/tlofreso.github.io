---
date: 2023-07-18
authors:
  - me
categories:
  - Home Lab
---

# My Home Network
In a past, I've used a variety of vendor provided hardware to work at the edge of my home network. 
They're all to eager to give you [free gear](https://meraki.cisco.com/en-uk/freemx/) 
in hopes of getting in the door of your employer. Anyways, it was high time I rolled my own HW.  

My decision was a toss-up between raw linux on a Pi, or OPNsense on a mini PC. I opted for the latter... 
Mostly due to the popularity around OPNsense / PFsense and some of the simplicity gains there. But also, Pi's 
are still hard to come by. A mini PC with more interfaces sounded great for a router, so I ordered a [Qotom Q750G5](https://www.amazon.com/Advanced-Firewall-Qotom-Mini-PC-Q750G5/dp/B0B28XWWHK)

!!! note

    Annectodally, if I were to pursue a Pi, I'd do something along these lines:  
      * [Jeff Geerling - Pi Simple Router](https://www.jeffgeerling.com/blog/2021/setting-raspberry-pi-2-network-interfaces-very-simple-router)  
     * [Tall Paul Tech - Pi Router and Firewall](https://youtu.be/7riJkp5q1-M)

The Qotom I ordered has 16GB or Ram, and a 64GB SSD. Memory has yet to crest 20% utilization in the several weeks I've been running this box. For a good overview of the hardware, and OPNsense installation see [this](https://0x2142.com/opnsense-qotom-q750gs/) post by a fellow network engineer.

