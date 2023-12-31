---
draft: true
date: 2023-07-18
authors:
  - me
categories:
  - Home Lab
---

# My Home Network
In the past, I've used a variety of vendor provided hardware to work at the edge of my home network. 
They're all to eager to give away [free gear](https://meraki.cisco.com/en-uk/freemx/){:target="_blank"} 
in hopes of getting in the door of your employer. Anyways, it was high time I rolled my own HW.  

My decision was a toss-up between raw linux on a Pi, or OPNsense on a mini PC. I opted for the latter... 
Mostly due to the popularity around OPNsense / PFsense and some of the simplicity gains there. But also, Pi's 
are still hard to come by. A mini PC with more interfaces sounded great for a firewall, so I ordered a [Qotom Q750G5](https://www.amazon.com/Advanced-Firewall-Qotom-Mini-PC-Q750G5/dp/B0B28XWWHK){:target="_blank"}
<!-- more -->
!!! note

    Annectodally, if I were to pursue a Pi, I'd do something along these lines:  
     * [Jeff Geerling - Pi Simple Router](https://www.jeffgeerling.com/blog/2021/setting-raspberry-pi-2-network-interfaces-very-simple-router)  
     * [Tall Paul Tech - Pi Router and Firewall](https://youtu.be/7riJkp5q1-M)

## The Hardware
A new firewall was the genesis of my home network upgrades, which ultimately included new wifi, servers, and switches.

### Firewall
The [Qotom mini PC](https://www.amazon.com/Advanced-Firewall-Qotom-Mini-PC-Q750G5/dp/B0B28XWWHK){:target="_blank"} has 16GB of Ram, and a 64GB SSD. Memory has yet to crest 20% utilization in the several weeks I've been running this box. For a good overview of the hardware, and OPNsense installation see [this](https://0x2142.com/opnsense-qotom-q750gs/){:target="_blank"} post by a fellow network engineer.

### Switches
My primary switch is a Ubiquiti [US-8-150W](https://store.ui.com/us/en/collections/unifi-switching-utility-hi-power-poe/products/us-8-150w){:target="_blank"}. Great because it's fanless, has an internal power supply, and will do up to 30W of PoE per interface with a total power budget of 140W. There are a number of 5-port switch [flex minis](https://store.ui.com/us/en/collections/unifi-switching-utility-mini/products/usw-flex-mini){:target="_blank"} around the house as well to provide wired connectivity wherever I'd like.  

Ubiquiti has had their fair share of rough spots over the years. I've been happy with the hardware, it's served me well a number of years... but come time to upgrade, I'll be hard pressed to buy from UI again for the following reasons: 

1. The TLDs...`www.ubiquiti.com` is not ubiquiti. They used to be `www.ubnt.com`... and you *can* still [find content](https://dl.ubnt.com/datasheets/unifi/UniFi_AP_DS.pdf){:target="_blank"} hosted under the ubnt TLD. They're now at [ui.com](https://ui.com){:target="_blank"} - the transition was [not smooth](https://community.ui.com/questions/UBNT-com-Changed-to-UI-com/491f0b28-4d2e-4dbd-9e83-72a10f1ddf50){:target="_blank"}.
2. Somewhere along the line they changed usernames to email addresses, which is fine, but also was not a smooth transition. 
3. The [breaches in '21](https://community.ui.com/questions/Account-Notification/96467115-49b5-4dd6-9517-f8cdbf6906f3){:target="_blank"} were fairly harrowing. Eventually involving the FBI, and DOJ which you can read more about [here](https://www.justice.gov/usao-sdny/pr/former-employee-technology-company-charged-stealing-confidential-data-and-extorting){:target="_blank"}.

Anyways, for the time being, the gear serves me well and I try to keep up with ubiquiti and their dynamic nature.

### Servers
Home lab "servers" are great these days. It's remarkable how much power / dollar you can buy. I purchased a pair of Dell [Optiplex 7050s](https://www.ebay.com/itm/195677048964?mkcid=16&mkevt=1&mkrid=711-127632-2357-0&ssspo=wBRUG_j0TW-&sssrc=2047675&ssuid=przliihetvw&widget_ver=artemis&media=COPY) off ebay for under $100. Way more headroom than a pi, and not a whole lot more spendy. I also run Pi-hole on a Raspberry Pi 4B. Everything's mounted on a board in my bsement, nice and tidy.

## The Software
I could (and maybe will) write a blog post for each piece of software identified here. These are all programs I've been wanting to run for somoe time, and finally this year I bought the servers, and allocated the time to do it.

### OPNSense
Right from [their site](https://opnsense.org/about/about-opnsense/){:target="_blank"} OPNsense is an:
> open source, easy-to-use and easy-to-build FreeBSD based firewall and routing platform  

I chose OPNsense over PFsense primarily because I know more people who run it. Updates are easy, and there's a built-in plugin marketplace that's quite handy. It's my stateful firewall, my DHCP server, and my wireguard VPN concentrator.

### Pi-hole
Man, what a [great project](https://pi-hole.net){:target="_blank"}. And the level of detail put into onboarding / installation deserves applause. The bane of many OSS projects, is the level of expertise they require to get up and running. Pi-hole is a rare exception. Installing and configuring the software couldn't be easier, and if you end up needing help, the community is vast.

### NetBox

### Prometheus / Grafana

### Home Assistant

### SDR



Other changes include:

- expanding the LAN from /24 to /22
- adding pihole for dns
- setting up wireguard