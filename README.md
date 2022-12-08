# digitaltwin

Building physical systems that provide data to a virtual environment is
a bit of a 'best of both worlds' idea, now ascendant on the hype
curve. Here is a little background on the history of the technological
development, using buzz-phrases. 


Some time ago -- early 2000s -- we saw the advent of wireless sensor networks (WSNs). 
The examples I am aware of originated in academia; I have no experience with 
military or industrial development since those tend away from the 'open'
paradigm. WSNs had the advantage of being novel and interesting and they
occupied a perfect ***brave new world*** niche. However they suffered from
the need for intensive effort to make them work; the technology was not
yet mature to make them easy to build and reliable. The most successful 
example I had direct experience with was the Life Under Your Feet project
(soil ecology) at Johns Hopkins. 


Roughly simultaneous with sensor networks we had advent of cloud computing; 
and since sensor network data had to live somewhere accessible, the cloud was
the obvious destination. This quickly gave rise to the buzz phrase Internet of 
Things (IOT). 


At roughly the same time (geologically speaking) we also saw the rise of the
Maker culture which helped standardize and ruggedize the small cheap computer
trend, particularly with Arduino on the low end and Raspberry Pi on the high 
end. The latter runs Linux and has the horsepower of a small workstation; 
and runs roughly $20 per unit. The profusion of open projects helped drive
this development; including an explosion of peripheral devices: Range sensors,
cameras, accelerometers, display screens, temperature and light sensors, 
and so on. We also have the advent of GitHub and Stack Overflow so we had
both a place to go for inspiration in detail; and a place to go to cry out
for help respectively. 


In the mid-2010s the trend naturally expanded to include the notion of 
devices that could actuate events; could cause things to happen. Such a
device became known as a Cyber-Physical System or CPS. The vanguard of 
this development was the profusion and refinement of drones and in 
parallel the development of autonomous vehicles. In geoscience we've 
had accompanying developments in platforms and instrumentation that 
continues through the present. 


So in terms of high visibility we have come through about two decades 
of rapid miniaturization and ruggedization of computing tools that 
originated as fragile creatures inhabiting air-conditioned labs, now
routinely crawling and flying across the surface of mars. 


Enter the digital twin, a recent buzz phrase intended to conjure
up a matrix-like system coupled to real data from the real world. 

## recipe: sensor - Arduino - cellular modem - cloud - twin


Let's suppose we have a light sensor attached to an Arduino analog port.
This gives us a value from 0 to 255 at a rate up to say kilohertz
sampling frequency.

Let's further suppose there are
actually three such units: **A**, **B** and **C**.


The goal is to string together ancillary parts and code so that they
are able to communicate both directly and indirectly.


### Direct communication

Each Arduino has a laser diode mounted on a servo in addition to a CdS 
light sensor. 

### Indirect communication

Each Arduino has ...


### refs

[modem_init()](https://github.com/naclomi/emojiomi/blob/main/device/firmware/main/main.ino)

The cell modem technology we will use here is called SIM7000. 

Search on **`sim7000 at commands`** to find the Users Manual.

Search on **`sim7000 tcpip`** to find the use case document.


See Figure 2 at [this link](https://www.google.com/url?sa=t&rct=j&q=&esrc=s&source=web&cd=&cad=rja&uact=8&ved=2ahUKEwilrumR5ej7AhU8CjQIHUUCBC8QFnoECA4QAQ&url=https%3A%2F%2Fsimcom.ee%2Fdocuments%2FSIM7000x%2FSIM7000%2520Series_TCPIP_Application%2520Note_V1.01.pdf&usg=AOvVaw3bO5eF1vb4eJi7zgeLCqi8)

Search SIM7000 Series_AT Command Manual
