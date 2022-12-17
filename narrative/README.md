# Digital twin project


Here we describe the building blocks that go into this digital twin demonstration project.
This includes both a conceptual framework and the build process.
The build process is described as two phases: First putting the pieces in place for 
two-way communication between a sensor system and the cloud; and second refining the code to 
support a demonstration scenario.


> Note: [Twilio](https://twilio.com) is used here simply as one possible provider.
> There are many commercial options available.


The premise of this project is a real-world sensor that records data to cloud storage.
This sensor system can also receive directives back from the internet; for example
from cloud-based agents or from other sensors. Within this messaging structure, Twilio 
acts as a paid (low cost) message brokerage service or 'middle man'.


Before getting into the narrative detail: Note that the website 
[twilio.com](https://twilio.com) provides general information; but taking action
using twilio resources requires a twilio login account. This account
provides access to the [**twilio console**](https://console.twilio.com).


### Development narrative


- Development described here originates from a 'development machine'
    - This is typically a laptop or desktop computer
    - We ran Windows; and other operating systems are fine
    

- We refer to the prototype sensor / actuation device as the *sensor*
    - For our example this consists of an Arduino UNO and a cellular modem shield
        - Cellular modem: [SIM7000A Arduino NB-IoT Expansion Shield](https://www.dfrobot.com/product-2170.html)
    - The sensor includes a photoresistor or comparable sensor of some type
    - The cellular modem communicates with the internet via the cellular network
    - The Twilio services facilitate this communication traffic
    - Communication involves both a network protocol and a transport protocol
        - Network protocol: **Internet Protocol (IP)**
        - Transport protocol: **User Datagram Protocol (UDP)**
        
        
- Cell phones communicate via a cellular network that includes cell towers studded with antennas
    - Network infrastructure is built out by multiple service providers: Verizon, T-Mobile etcetera
    - In this project we do not interact with these service providers directly
    - We *do* interact with Twilio by means of its service APIs
    
    
- Twilio services
    - The technology we use for passing small data packets is called 'Super SIM' by Twilio
    - The communication protocol is [Internet Protocol (IP)](https://en.wikipedia.org/wiki/Internet_Protocol)
        - The corresponding command set is therefore 'IP commands'
    - Device authentication is by means of Super SIM cards
        - A SIM card includes an integrated circuit with unique authentication credentials
        - It is convenient to purchase Super SIM cards from Twilio
            - Cards come pre-registered to a Twilio account ($3.00 per SIM card)
            - To purchase: **Twilio console** > **Develop** tab > **Internet of Things** > **Order** > **Order**
            - Cards are delivered as 'three concentric form-factors in one' (mini / micro / nano)
        - Use the Twilio console to review SIM card status
            - **Console** > **Develop tab** > **Internet of Things** > **Super SIM** > **SIMs**
        - Use the Twilio console to monitor message traffic
            - **Console** > **Monitor tab** > **Logs** > **Super SIM** > **IP Commands tab**
    - Super SIM cards are plugged into a cellular modem integrated in the sensor
    
    
- Development process: Location, means and purpose
    - There are four 'locations of activity' and corresponding means of interaction
        - (1) The sensor (means: [Arduino IDE](https://docs.arduino.cc/software/ide-v2))
            - An Arduino program ('sketch') is written in the IDE and transferred to the Arduino device
            - The Arduino IDE also features a terminal-like serial interface to the Arduino
        - (2) The Twilio console (means: browser interface)
            - Uses menu structure to navigate Super SIM development and monitoring actions
        - (3) Twilio command line interface (means: Application running on the development computer)
            - Authenticates under the established Twilio account
            - Can be used to pass test messages to the sensor
        - (4) Cloud serverless application (means: Cloud console; other paths available as well)
            - Authenticate to a cloud account, e.g. on AWS, GCP or Azure
            - Establish a connection URL
            - Establish code to parse incoming messages from sensors and other sources
            - Establish code to generate messages to sensors and other recipients
            - Establish a means of storing data
            
- Development process part 1 outline
    - Establish twilio account, get Super SIM cards
    - Create twilio ***fleet*** and ***network access profile (NAP)***
    - Secure Arduino, cellular modem, power supply, sensor 
    - Use the Arduino IDE to program a cellular modem test sketch
    - Use the Arduino IDE serial port connection
        - Using manual commands configure the cellular modem
        - Using manual commands send test messages to Twilio
    - On the cloud of choice establish an API URL
        - Register this URL with Twilio using the Twilio console
        - Connect this URL to a serverless function
        - Configure the serverless function to parse inbound messages
    - From the Sensor: Transmit a test message as above
        - On the cloud: Confirm the test message was received
        - Unpack the message to get a clear view of the message structure
    - Using the Twilio command line interface send a message to the sensor
        - Verify on the Twilio console this message was received
        - Verify on the Sensor this message was received
    - Modify the cloud serverless function to reply to a Sensor message
        - Send a test message from the Sensor to Twilio
            - This will be relayed to the cloud URL
            - The cloud function will generate and send a response
            - This response will be routed by Twilio to the Sensor
            

From this point on development will concentrate on the Sensor program and 
the cloud serverless function. 


- Development process part 2 outline
    - Modify the Arduino sketch
        - Above: Interactivity was manual
        - Here: The object is to make it automated
        - Arduino initiates cellular connection
            - Also deal with loss of connection
        - Arduino initiates messages to the
            - Also deal with a confirmation reply
    - Modify the cloud application
        - Above: Response to inbound messages: send acknowledgement
        - Here: Initiate messages in response to other triggers
            - See section below on 'other triggers'
            
            
### Other triggers

Serverless functions are triggered by events. This is in contrast to an execution stream
that runs continuously and occasionally originates messages of its own accord. 
