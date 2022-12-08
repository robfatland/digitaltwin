# digitaltwin

## merge a slow-rate sensor, Arduino, cellular modem, cloud

Let's suppose we have a light sensor attached to an Arduino analog port.
This gives us a value from 0 to 255 on demand. Later on perhaps this 
is augmented by an accelerometer. Furthermore let's suppose there are
two more units in the same sense, so together **A**, **B** and **C**.


The goal is to string together ancillary parts and code so that they
are able to communicate both directly and indirectly.


### Direct communication

Each Arduino has a laser diode mounted on a servo in addition to a CdS 
light sensor. 

### Indirect communication

Each Arduino has ...


### refs

[modem_init()](https://github.com/naclomi/emojiomi/blob/main/device/firmware/main/main.ino)

See Figure 2 at [this link](https://www.google.com/url?sa=t&rct=j&q=&esrc=s&source=web&cd=&cad=rja&uact=8&ved=2ahUKEwilrumR5ej7AhU8CjQIHUUCBC8QFnoECA4QAQ&url=https%3A%2F%2Fsimcom.ee%2Fdocuments%2FSIM7000x%2FSIM7000%2520Series_TCPIP_Application%2520Note_V1.01.pdf&usg=AOvVaw3bO5eF1vb4eJi7zgeLCqi8)

Search SIM7000 Series_AT Command Manual
