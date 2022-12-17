# digitaltwin


* [Project background](https://github.com/robfatland/digitaltwin/tree/main/background)
* [Development narrative](https://github.com/robfatland/digitaltwin/tree/main/narrative)


<img src="https://github.com/robfatland/digitaltwin/blob/main/i/testbed.png" alt="drawing" width="400"/>


***Arduino-based development testbed***


This repository is a circa-2022 recipe for combining a
simple sensor with a cellular modem for two-way communication with the cloud.
It is motivated by the 'digital twin' concept and its technological antecedent
'Internet of Things' (IOT).


The documentation tries to follow two guidelines


- Minimize content to 'must know' 
- Place deeper information into ancillary documents


## Recipe

### Sensor - Arduino - cellular modem - broker - cloud - digital twin


Suppose we are interested in measuring ambient light levels at some
location over time. A microcomputer such as an Arduino or a Raspberry Pi
can digitize a voltage mediated by a cadmium sulfide
[photoresistor](https://en.wikipedia.org/wiki/Photoresistor)
for this purpose. The question is then how to recover
the observational data. This **Cloud Solution** 
repository describes a particular means that relies upon
cell phone infrastructure. The resulting system is reliable,
low cost to build and operate, and requires little maintenance. 
Deployment is constrained
to locations with
[cell phone coverage](https://www.fcc.gov/BroadbandData/MobileMaps/mobile-map).
Our anticipated data rate is low in comparison to that used
in voice communication. As a result we use a cheaper mechanism
that piggybacks on
the cellular network infrastructure using a communication protocol called the 
User Datagram Protocol ([UDP](https://en.wikipedia.org/wiki/User_Datagram_Protocol)).



Suppose we embark on a 'digital twin'
project that depends upon data from three autonomous 
sensor assemblies **A**, **B** and **C** built on the Arduino
stack. We configure them to report sensor measurements to
the Amazon Web Services ('AWS'), 
Google Cloud Platform ('GCP') and 
Microsoft Azure ('Azure') clouds respectively. 
Each cloud will send control guidance back 
to the sensors to demonstrate two-way communication.



Additional technology is required between the Arduino and
the cloud to complete
this construction. We use a cellular modem in the form
of an Arduino shield (an add-on circuit board) to connect
to the cellular network. Specifically this cellular modem 
uses a  SIM card to connect to, authenticate, and send and receive data. 
While our data transfer uses commercial assets owned
by T-Mobile, Verizon etcetera, 
we do not establish contracts with commercial cell phone 
carriers. Instead we establish a contract with a
third party company that provides programmable communication tools
for sending and receiving data. This company (Twilio) in turn
establishes necessary permissions with the commercial carriers. 
In effect Twilio acts as both a middle man between us and the 
commercial carriers, and as a message broker between our 
Arduino device and the public cloud. 


Messages that arrive
at the cloud from the Arduino are necessarily parsed by
software we supply. This software is referred to 
herein as *serverless* because it is run as a service
with no underlying server visible to us, the project developers. 


The features of the prototype system described here include
low cost, low maintenance effort and high reliability.


- A prototype system will cost less than $100 to assemble
- This system costs less than $5 per month to operate
    - Cost includes cloud storage of sensor data
- Cloud data storage is extremely stable and reliable
- Data transfer is low maintenance
    - It is built on *services* rather than owned hardware
        - There are no servers, no network cards etcetera
- There is no dependency on WiFi 
- Maintenance effort centers on the Arduino-based sensor
    - It requires a stable power supply
    - Ruggedization is generally necessary
    - Research projects evolve over time
        - This may result in modifying the Arduino system
            - Change the hardware configuration
            - Re-program the Arduino
            - Re-program the cloud-based serverless data parser
- Data on the cloud can be made selectively accessible
    - ...to collaborators
    - ...to other approved third parties


<img src="https://github.com/robfatland/digitaltwin/blob/main/i/digitaltwin_signal_path.png" alt="drawing" width="500"/>

## Pivot to project description


The remainder of this document provides a 'build recipe' in outline form
with references to supporting detail documentation.


### Sources


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

### Arduino Power

The Arduino board can operate on power delivered by a USB cable. However the DFRobot cellular modem
can draw power in excess of what is available by this means. Therefore

- Obtain a 'barrel plug' power supply to power up the Arduino + DFRobot SIM7000 shield
- Attach a USB cable from the development computer to the Arduino


### Library installation

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

### Twilio monitor 

First step in establishing the from/to communication: Arduino to twilio.

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



### Twilio command line interface


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


> It would be helpful to sort out *who* IP commands go to: Twilio or the Arduino or the shield.


#### Motivation and Digression


##### This should go into an ancillary


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


### From Configuration to Communication


This section outlines the process of getting two-way communication running 
programmatically between the Arduino and the cloud.


#### Twilio
    
    
##### This should go into an ancillary
    
    
##### Expand on what is an NAP and etc: Better capture on second time around
    

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
            
    

#### Serverless on cloud

##### Instructions for revising this section:
    
- Abstract-ize this to the punchline of a URL
- Move AWS-specific details down lower
- Outline the delta between research code and Naomi code


Some example code for building the AWS component of the signal chain is
[here](https://github.com/naclomi/emojiomi/blob/main/infra/api/device_message/lambda_function.py).
Focus on the `data[]` structure: That will be the payload. 


Use API Gateway to establish a URL for messages from Twilio. Twilio will be acting as 
a relay agency, passing messages between the Arduino device and the Lambda function. 
When we move on to using Azure and GCP we will need additional vocabulary for equivalent
services.


Upon completing the Lambda: Return to the Twilio entry for the SIM and change the
connection URL to that of the Lambda API Gateway.


#### Interactive session: IDE to cellular modem


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


#### Test: Messaging from the twilio app to the Arduino

    
##### To ancillary
    

Go to [this link](https://www.twilio.com/docs/iot/supersim/get-started-with-super-sim-ip-commands#send-ip-commands-to-the-device).


Note that this Get Started guide is broken into sections. Follow Section 3 to test 
send an IP Command from a computer to the Super SIM. The text should show up in the 
IDE Serial Monitor. 


#### Programmatic use of the cellular modem

##### Ancillary
    
Update the sketch to send a simple payload periodically to twilio.

This should forward to the Lambda function.

Have Lambda send something back.

This should appear in the Serial window.


##### Making a Lamda layer


Make a development folder in the Linux environment.

```
mkdir dtwin; cd dtwin
```

Here: Edit `requirements.txt` to look like this: 

```
twilio
pyjwt
jinja2
bcrypt
```

This is perhaps more than is needed. twilio is the main point. 

Create a sub-folder called Python

```
mkdir python
```

Build the requirements into this folder:

Zip the results

```
zip -FS -r ./python.zip ./python
```

Move this into a Windows folder where it can be accessed by an Upload wizard.

```
mv python.zip /mnt/c/Users/kilroy/Downloads
```

- Log in to the AWS console > Set your region > Lambda > Layers > Create Layer
- Configure this wizard
    - Name: `digitaltwin_requirements`
    - Description: List the requirements.txt libraries `twilio` etcetera
    - Runtimes: Select ***ALL*** Python runtimes, not just one
    - Create
- Lambda > choose Lambda > put this code in the code window


```
import base64
import json
import time

import boto3
import botocore.exceptions
from decimal import Decimal

import os

import twilio.rest

acct_id_env = os.environ['acct_id_env']
acct_token_env = os.environ['acct_token_env']

def lambda_handler(event, context):
    print(str(event))
    print(base64.b64decode(event["queryStringParameters"]["Payload"]).decode())
    
    # print(str(event["queryStringParameters"]))
    el_sim = event["queryStringParameters"]["SimUniqueName"]
    # data = base64.b64decode(event["queryStringParameters"]["Payload"]).decode()
    # print(data)


    client = twilio.rest.Client(acct_id_env, acct_token_env)
    result = client.supersim.v1.ip_commands.create(
        sim=el_sim,
        device_port=6969,
        payload="cloud says what"
    )

    return {
        'statusCode': 200,
        'payload': str(event)
    }
```

- Set up an API Gateway, link to this Lambda function
- Lambda function > Scroll to bottom of Code > Layers > Add Layer > Custom layers > Choose the layer added above
- Lambda function > Configuration > Environment variables > Edit > Add two variables from the twilio account

These are the User SID and the access token

```
acct_id_env          123456789012345678901234567890123456789012345678901234567890
acct_token_env       ABCDEFGHIJKLMNOPQRABCDEFGHIJKLMNOPQRABCDEFGHIJKLMNOPQRABCDEFGHIJKLMNOPQR
```


Notice these are picked up in the Lambda code using `acct_id_env = os.environment['acct_id_env']`.


# Unsorted content


* [Super SIM IP command reference](https://www.twilio.com/docs/iot/supersim/get-started-with-super-sim-ip-commands]
* [Twilio CLI installation](https://www.twilio.com/docs/twilio-cli/getting-started/install#windows)
* **`sudo apt install tree`** installs a useful tree-view version of recursive `ls` called, of course, `tree`


##### AWS Serverless Functions


- Serverless functions on AWS are called *Lambda functions*
    - Very low cost per thousands of executions (compare 86400 seconds per day)
- An AWS Lambda function executes in response to a *trigger*
    - We use a separate service as a trigger, called an API Gateway
        - The API Gateway provides an internet-accessible URL
	- This URL will be registered with Twilio for routing sensor-originating messages
	- This URL will also be used by a Python program running on the development system




#### Azure Serverless Functions



#### Google Cloud Serverless Functions
    
