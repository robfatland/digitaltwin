# digitaltwin


<img src="https://github.com/robfatland/digitaltwin/blob/main/i/testbed.png" alt="drawing" width="500"/>


The purpose of this repository is to provide a circa-2022 recipe for building a
simple sensor with a cellular modem for two-way communication with the cloud.




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
