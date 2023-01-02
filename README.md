# A digital twin project


> Building a two-way communication path between a sensor and a cloud-based data system


* [Project background](https://github.com/robfatland/digitaltwin/tree/main/background)
* [Development narrative](https://github.com/robfatland/digitaltwin/tree/main/narrative)
* [temp: graphics development](https://docs.google.com/presentation/d/1bdB73Bvjc2u0iwq2IxrxCuNwtDI0Kh2ppTXeTmgdbrI/edit#slide=id.g1b4fc1ac9c2_0_0)


<img src="https://github.com/robfatland/digitaltwin/blob/main/i/testbed.png" alt="drawing" width="400"/>


***Arduino-based development testbed***


This repository is a circa-2022 recipe for combining a
simple sensor with a cellular modem for two-way communication with the cloud.
It is motivated by the 'digital twin' concept and its technological antecedent
the 'Internet of Things' (IOT).


The documentation tries to follow two guidelines


- Minimize content to 'must know' 
- Place deeper information into ancillary documents


## What are we signing up to learn? 


In the interest of not making this seem easier than it is, the answer is 'Rather a lot, in fact.' 
None of the component pieces is terribly difficult to master. However
if these tools and technologies are unfamiliar then we are looking at a considerable time
investment, on the order of weeks initially; followed by further refinement of the picture
over the course of a research project. A sketch of what's-to-learn follows, and a 
more elaborated version can be found [here](https://github.com/robfatland/digitaltwin/tree/main/narrative).


- A development environment and programming language for an IOT device (here: Arduino UNO)
    - ...including a sensor interface that produces some form of recognizable data (here: a light sensor)
    - ...and possibly an actuator (here: a laser diode and a servo motor)
- The interface / communication protocol from the IOT device to a cellular modem (here: Internet Protocol and User Datagram Protocol)
- A browser-based interface to a middleman message service (here: Twilio)
- A browser-based console interface to a public cloud provider (here: AWS and eventually also Azure and GCP)
    - ...leading to standing up three essential integrated cloud components
        - An internet-facing API service for messaging, cloud < -- > IOT device (AWS API Gateway, GCP API Gateway, Azure API Management)
	- A NoSQL database service (AWS DynamoDB, GCP Bigtable, Azure Cosmos DB)
	- A serverless function connecting IOT sensor messages to this database (AWS Lambda, Azure Functions, Google Cloud Functions)
- Diagnostic and debugging skills; includes working familiarity with:
    - The cellular modem command set
    - NoSQL `put`, `get`, `query`
    - Cloud serverless function design and debugging (here: using Python)
    - IOT sensor IDE (here: the Arduino IDE uses a C/C++ 'dialect')
    - Communication service console (here: Twilio)


Again: A more granular view is available as '[narrative notes](https://github.com/robfatland/digitaltwin/tree/main/narrative)'.


## Recipe: Sensor > ... > Database > .... > Sensor


A [digital twin](https://en.wikipedia.org/wiki/Digital_twin) is technically an 
*in silico* model that 'twins up' with a real world environment of interest. 
Connectivity between them generally involves some collection of sensors. 


This repository is concerned with the link between sensors and the digital twin. We
are interested in this link working in both directions to accommodate actuation commands 
from the digital twin to the sensor array. Incidentally I will use 'IOT sensor' to 
indicate a microcomputer-sensor-modem assembly.


Suppose we are interested in measuring ambient light at some
location over time. A microcomputer such as an Arduino or a Raspberry Pi
can digitize a voltage mediated by a cadmium sulfide
[photoresistor](https://en.wikipedia.org/wiki/Photoresistor)
for this purpose with the communication path riding along both
the cellular network and the internet.
This solution is reliable,
low cost to build and operate, and requires little maintenance. 
On the down side, deployment of sensors is constrained
to locations with
[cell phone coverage](https://www.fcc.gov/BroadbandData/MobileMaps/mobile-map).


We want to acknowledge the profusion of jargon for a moment here.
As an example the terms 'UDP' and 'IP' arise where both P's stand for 'protocol'.
Learning the intricacies of these protocols should be *optional*. The practical
approach we try for is to identify and hopefully de-mystify jargon and indicate 
an initial 'minimum viable familiarity' necessary. Hence: 
Our solution here has a presumed low required communication bandwidth (say relative to 
voice communication). This allows us to pay for a cheap service that makes 
use of cellular network infrastructure. This service
uses [UDP](https://en.wikipedia.org/wiki/User_Datagram_Protocol)
and [IP](https://en.wikipedia.org/wiki/Internet_Protocol)
for the transport and internet layers respectively.
Practically speaking these fairly simple protocols drive message construction 
and operational debugging.



The 'demonstrator' system described here will include 
three IOT sensors based on the low-cost Arduino microcomputer.
The tradeoff (say compared to using a Raspberry Pi) is that
an Arduino UNO does not have a lot of native processing power 
or memory.
We configure the three IOT sensors to report to
the Amazon Web Services ('AWS'), 
Google Cloud Platform ('GCP') and 
Microsoft Azure ('Azure') clouds. 




The Arduino is attached to a cellular modem (an Arduino 
add-on circuit board or 'shield'). The cellular modem 
uses a  SIM card to connect to, authenticate, and send and receive 
data via the cellular network. 
Our system will make use of commercial infrastructure from
T-Mobile, Verizon, AT&T and other carriers; but 
we do not need to establish contracts with each of these
commercial cell phone carriers. Rather we establish a single
contract with a third party company called *Twilio* that provides programmable 
communication tools for sending and receiving data. 
Twilio in turn handles relationships with the 
commercial carriers; so Twilio is both the 'middle man' 
between us and commercial carriers, and also acts as 
a message broker connecting the cellular network and
the internet. We establish a cloud end-point (URL) 
and register this with Twilio.


Messages that arrive
at the cloud from the Arduino are necessarily parsed by
software we supply. This software is referred to 
herein as *serverless* because it is run as a service
with no underlying server visible to us, the project developers. 


Solution features include:


- A prototype system will cost less than $100 to assemble
- This system costs less than $5 per month to operate
    - Cost includes cloud storage of sensor data
- Cloud data storage is extremely stable and reliable
- Data transfer is low maintenance
    - It is built on *services* rather than owned hardware
        - There are no servers, no network cards etcetera
- The system has no dependency on WiFi 
- Maintenance centers on the (possibly ruggedized) Arduino-based IOT sensor
    - This requires a stable power supply
    - Research projects tend to evolve
        - Hence: Modify the IOT sensor
            - hardware configuration
            - Arduino operating code
        - Modify the corresponding cloud-based data system 
- Data on the cloud can be made selectively accessible...
    - ...to collaborators
    - ...to other approved third parties


<img src="https://github.com/robfatland/digitaltwin/blob/main/i/digitaltwin_signal_path.png" alt="drawing" width="500"/>


# How it should go from here


Write a section here that directs in sequence to

- UNO, IDE, modem, sensor build
- Twilio configuration
- AWS component


Then rebuild the subsequent material into those pages.


## Resource links


- An Arduino UNO R3 runs USD30 or about USD45 as a 'starter kit' with some useful peripheral hardware
- The cellular modem used here is available from DFRobot for USD40
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



<img src="https://github.com/robfatland/digitaltwin/blob/main/i/ATflow.png" alt="drawing" width="400"/>


## Arduino Power


The Arduino board can operate on power delivered by a USB cable. However the DFRobot cellular modem
can draw power in excess of what is available by this means. Therefore


- Obtain a 'barrel plug' power supply to power up the Arduino + DFRobot SIM7000 shield
- Attach a USB cable from the development computer to the Arduino


## Arduino Library installation


In the Arduino IDE: Use the IDE Tools > Manage Libraries... menu to start the Library Manager on the left side of the IDE.
Enter **`DFRobot_SIM7000`** in the search bar and install this library as well as any dependencies per the prompt.


<img src="https://github.com/robfatland/digitaltwin/blob/main/i/arduino_ide_port_chooser.png" alt="drawing" width="400"/>


Connect a laptop USB port to the Arduino USB port.
After physically connecting to the Arduino: Use the IDE Tools > Port menu to enable the port connection to the Arduino.


<img src="https://github.com/robfatland/digitaltwin/blob/main/i/arduino_ide_example_sketches.png" alt="drawing" width="400"/>


### ATTest sketch

```
ATE0
AT+CMEE=2
AT+CMNB=1
AT+CNMP=38
AT+CIPMUX=1
AT+CGDCONT=1,"IP","super"
AT+CSTT="super","",""
AT+CEREG?
AT+CIICR
AT+CIFSR
AT+CLPORT=0,"UDP",6969                          // Connection 0: Returns an ip address: Must have; but not usable
AT+CIPSTART=0,"UDP","100.64.0.1","6969"         // ip address is Twilio's gateway. Again connection 0
AT+CIPHEAD=1                                    // Identify data packets and their length
AT+CIPSEND=0,8                                  // message will be 8 bytes
actually                                        // serial interface counts chars as bytes; will cut out after 8
                                                // early terminate: Send a 0x1a ctrl-z character; but this is not
                                                //   supported by the serial connection from the IDE to the Arduino
                                                //   so this is where the sketch can be clever: Interpret a backtick
                                                //   character as 'send ctrl-z (0x1a)' and this terminates the send.
```


## Twilio monitor 

First step in establishing the from/to communication: Arduino to Twilio.

* Establish a Twilio account
* Log in to the twilio.com console
* Select at upper left the **Monitor** tab
* Expand **Logs**, select **Super SIM**
* Main window select **IP Commands**
* From the Arduino IDE: Use the Serial Monitor to send a string
    * Example: Above we have **`AT+CIPSEND=0,8`** followed by **`actually`**
* This string should show up in the message log
    * Direction should be From SIM
    * Payload something like Binary (base64 encoded) YWN0dWFsbHk=
        * Can go to [this link](https://www.base64decode.org/): Decode, verify it matches
    * Status is Received with a time stamp



## Twilio command line interface


Second step in establishing the from/to communication: twilio to Arduino.


We now install and use a command line interface 
that runs on our Dev box.  This will send signals through the internet to Twilio 
where they in turn route the message to our Arduino device. With the Arduino in a connected
state it will receive the message and echo the characters out to the serial 
connection from the Arduino to the Arduino IDE (serial monitor). So when the 
message appears in the serial monitor we have successfully sent data to our device.


* Locate the SID and Token provided by Twilio when setting up the Twilio account
* Locate the name of the Arduino device as it was registered at Twilio: I used **`a`**.
* Find the Twilio install page by searching on 'twilio cli install'
* For me on Windows: Install the Windows version
* Start a Command Prompt
* type **`twilio`**: Get a usage message
* **`twilio login`**
    * Paste in SID and token
    * Give a shorthand identifier like **`kilroy`**
    * Should not have to do this frequently
* **`twilio profiles:use kilroy`**
* **`twilio api:supersim:v1:ip-commands:create --sim a --payload "calculon says yes" --device-port 6969`**



How to discover this command format? First, emphasis: We use **IP** commands for SuperSIM 
UDP communication. The cheat sheet for twilio IP commands is found at
[this URL](https://www.twilio.com/docs/iot/supersim/get-started-with-super-sim-ip-commands)
Part 3 of this document has an example of sending an IP command to the device.





# Add'l source material


### To Do


- Expand Lambda a bit to provide a testable return directive
- Create another Lambda that initiates contact to the Arduino (e.g. set servo angle)
- Build an S3 with a CSV file that can be appended by yet a third Lambda
- Out of curiosity what do the various libraries 'not in use' do? (Decimal etcetera)
- Reorganize material to an AWS sub-folder here





> It would be helpful to sort out *who* IP commands go to: Twilio or the Arduino or the shield.



We want to issue commands to the cellular modem, eventually to include 'send this string to an endpoint
on the internet'. To do this we talk to the Arduino via serial port; and the Arduino in turn passes the 
message along to the cellular modem via a second serial port. The traffic goes both ways: The cellular
modem replies with some message (2nd serial connection) which the Arduino sends back (first serial
connection) to the Arduino IDE. This works great with one important exception: 0x1a is ctrl-z and is
interpreted by the SIM7000 cellular modem as '\<end of message\>' but there is no way to send this 
from the keyboard / IDE to the Arduino. Eventually we modify the `ATTest` sketch (which is doing this 
simple message passing in its execution loop) to use a different keyboard character, 
probably the backtick **`\``**, and pass this along as 0xa1. 


With this in mind we load the ATTest sketch for manual communication with the SIM7000.


Open an example sketch: Use the IDE File > Examples > DFRobot_SIM7000 menu to select sketch **`DFRobotSIM7000ATTest`**.
This sketch is the model for how to build cellular modem interactivity into the Arduino when it runs autonomously. 
This is one of two such modes, the other being interactively working with the cellular modem by means of a serial
connection. This is built into the IDE and it is enabled next.


<img src="https://github.com/robfatland/digitaltwin/blob/main/i/arduino_ide_serial_monitor.png" alt="drawing" width="500"/>


The IDE has a 'circle and dots' icon at the upper right. Use this 
(or ctrl + shift + M) to open the Serial Monitor as a panel at the bottom
of the IDE. Note it is part of a tabbed selector. Configure it to use **Both NL & CR** and **9600 baud**. The monitor
can now be used to test **`AT`** cellular modem commands interactively. As noted above:
We type in strings, hit return; the string goes to the Arduino and is passed along
to the SIM7000; and vice versa we see the response message.

    
    

## Interactive session: IDE to cellular modem


Use [this walk-through](https://github.com/robfatland/digitaltwin/blob/main/AT.md) 
to test the cellular modem and become familiar with how the commands work.


With the prior steps completed (Twilio is prepared to recognize your SIM):
Run through the commands in Part 3 in sequence to establish the connection
between your Arduino and the Twilio service. 


Expand on the idea that the inbound-to-Twilio should be diagnose-able. 
It is: Find the Monitor / IP commands for SuperSIM using the console and
the messages should appear there; going in both directions (twilio cli to 
Arduino, vice versa). The Arduino message will be base64 encoded so 
use the translator.


- est: Messaging from the twilio app to the Arduino


Go to [this link](https://www.twilio.com/docs/iot/supersim/get-started-with-super-sim-ip-commands#send-ip-commands-to-the-device).


Note that this Get Started guide is broken into sections. Follow Section 3 to test 
send an IP Command from a computer to the Super SIM. The text should show up in the 
IDE Serial Monitor. 




- Expand on what is an NAP and etc
- Set up an account at [twilio.com](https://twilio.com)
- Purchase some SuperSIM cards; this example project uses three of them
    - Printed on the card is a long identifier code
        - Note the last 4 digits of the Super SIM code you are using
- Configure Twilio to be ready to receive messages from the cellular modem
    - This in turn permits us to register and receive a Twilio-internal ip address
    - Log in to twilio.com > IoT > Super Sim > SIMS
    - Use the digits noted above to identify and select your SIM card
        - The resulting configuration wizard requires a defined Fleet
            - Create a Fleet: It requires an NAP
                - Create an NAP; use the 'instant global' option
            - Use a dummy Lambda URL for your SIM (temporarily)
            - Choose HTTP GET
                - Later this might necessarily be HTTP POST
        - Set the SIM status to **Ready**
            - We use IP commands, not SMS
            - Also need a note on the UDP protocol
        - Return to the SIMS dashboard and confirm your SIM is ready to go
- Abstract-ize this to the punchline of a URL
- Outline the delta between research code and NA code
- [NA ref code](https://github.com/naclomi/emojiomi/blob/main/infra/api/device_message/lambda_function.py).
    - Focus on the `data[]` structure: That will be the payload. 
- Use API Gateway to establish a URL for messages from Twilio. 
    - Twilio will be acting as a relay agency, passing messages between the Arduino device and the Lambda function. 
    - When we move on to using Azure and GCP we will need additional vocabulary for equivalent services
    - Maybe this is one fleet per cloud provider
    - The Twilio destination might have been left blank: So redirect to API Gateway



* [Super SIM IP command reference](https://www.twilio.com/docs/iot/supersim/get-started-with-super-sim-ip-commands)
* [Twilio CLI installation](https://www.twilio.com/docs/twilio-cli/getting-started/install#windows)
* **`sudo apt install tree`** installs a useful tree-view version of recursive `ls` called, of course, `tree`


- [AWS](https://github.com/robfatland/digitaltwin/blob/main/aws/README.md)
	- Serverless functions on AWS are called *Lambda functions*
	    - Very low cost per thousands of executions (compare 86400 seconds per day)
	- An AWS Lambda function executes in response to a *trigger*
	    - We work with two triggers: A Test trigger and an API Gateway trigger
		- Test triggers are configured and run from the Lambda designer pages of the AWS console
		- API Gateway triggers originate from a separate AWS service, the **`API Gateway`**
		    - The API Gateway provides an internet-accessible URL
		    - This URL is registered with Twilio for routing IOT sensor-originating messages
		    - This URL is also used by a Python program running on a development system
	- The [AWS-specific cloud configuration notes are here.](https://github.com/robfatland/digitaltwin/blob/main/aws/README.md)

    
