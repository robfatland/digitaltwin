# digitaltwin


<img src="https://github.com/robfatland/digitaltwin/blob/main/i/testbed.png" alt="drawing" width="500"/>


This repository provides a circa-2022 recipe for a
simple sensor with a cellular modem for two-way communication with the cloud.
The idea is to minimize the need-to-know and place some additional
context in lateral digressions like
[this one](https://github.com/robfatland/digitaltwin/tree/main/i).




## recipe: sensor - Arduino - cellular modem - cloud - twin


Let's suppose we have a light sensor attached to an Arduino analog port.
(See testbed image above.)
This port can be read to yield a value from 0 to 255 indicating how much
light is hitting the sensor. The sampling frequency can be anywhere 
up to say kilohertz. Let's further suppose there are
actually three such units: **A**, **B** and **C** and that each has
a laser diode mounted on a servo motor. The initial idea (for a
marginally non-trivial system) is to set each device up to hit the
light sensor of the next in a triangle; and to have them report to
the Amazon, Google and Azure clouds their current status. The 
clouds in turn may provide some directive guidance back to **A**, 
**B** and **C**.



### References

- The cellular modem used here is available from DFRobot for $30
    - Product name: SIM7000 Arduino NB-IoT/LTE/GPRS expansion shield
    - [Product link](https://wiki.dfrobot.com/SIM7000_Arduino_NB-IoT_LTE_GPRS_Expansion_Shield_SKU__DFR0505_DFR0572)
    - Use [LTE](https://en.wikipedia.org/wiki/LTE_(telecommunication)): A wireless broadband communication standard for mobile devices
        - Does not use GNSS (navigation system)
- The cellular modem has a command vocabulary
    - Each command is prefaced by uppercase **`AT`** which historically abbreviated "Attention!"
    - These commands are sent to the SIM7000 shield
        - During development: From the Arduino IDE; and from the Arduino sketch
        - During operation: From the Arduino sketch
- Modem command vocabulary documentation
    - Search **`sim7000 AT commands`** > [User Manual](https://www.google.com/url?sa=t&rct=j&q=&esrc=s&source=web&cd=&cad=rja&uact=8&ved=2ahUKEwiXu--4zOr7AhX-MDQIHbWzAmMQFnoECBAQAQ&url=https%3A%2F%2Fsimcom.ee%2Fdocuments%2FSIM7000x%2FSIM7000%2520Series_AT%2520Command%2520Manual_V1.04.pdf&usg=AOvVaw3YaMs0QRpt9-A082_i6Ky3)
    - Search **`sim7000 tcpip`** > [Use case document](https://www.google.com/url?sa=t&rct=j&q=&esrc=s&source=web&cd=&cad=rja&uact=8&ved=2ahUKEwilrumR5ej7AhU8CjQIHUUCBC8QFnoECA4QAQ&url=https%3A%2F%2Fsimcom.ee%2Fdocuments%2FSIM7000x%2FSIM7000%2520Series_TCPIP_Application%2520Note_V1.01.pdf&usg=AOvVaw3bO5eF1vb4eJi7zgeLCqi8)
        - Figure 2 reproduced below gives a sense of the cellular modem command vocabulary
- Modem command code resource
    - See the modem_init() function [here](https://github.com/naclomi/emojiomi/blob/main/device/firmware/main/main.ino)
        - These commands can be entered manually via the IDE during development



<img src="https://github.com/robfatland/digitaltwin/blob/main/i/ATflow.png" alt="drawing" width="500"/>


Library installation: Use the IDE Tools > Manage Libraries... menu to start the Library Manager on the left side of the IDE.
Enter **`DFRobot_SIM7000`** in the search bar and install this library as well as any dependencies per the prompt.


<img src="https://github.com/robfatland/digitaltwin/blob/main/i/arduino_ide_port_chooser.png" alt="drawing" width="500"/>


Connect a laptop USB port to the Arduino USB port.
After physically connecting to the Arduino: Use the IDE Tools > Port menu to enable the port connection to the Arduino.


<img src="https://github.com/robfatland/digitaltwin/blob/main/i/arduino_ide_example_sketches.png" alt="drawing" width="500"/>


Open an example sketch: Use the IDE File > Examples > DFRobot_SIM7000 menu to select sketch **`DFRobotSIM7000ATTest`**.
This sketch is the model for how to build cellular modem interactivity into the Arduino when it runs autonomously. 
This is one of two such modes, the other being interactively working with the cellular modem by means of a serial
connection. This is built into the IDE and it is enabled next.


<img src="https://github.com/robfatland/digitaltwin/blob/main/i/arduino_ide_serial_monitor.png" alt="drawing" width="500"/>


The IDE has a 'circle and dots' icon at the upper right. Use this to open the Serial Monitor as a panel at the bottom
of the IDE. Note it is part of a tabbed selector. Configure it to use **Both NL & CR** and **9600 baud**. The monitor
can now be used to test **`AT`** cellular modem commands interactively.


## Interactive session IDE to cellular modem


```
AT
```
should produce **`OK`**.

```
AT+CIPSTATUS
```
should produce **`OK   STATE: IP INITIAL`** and some time (a minute) later: **`+CPIN: READY    SMS Ready`**.
