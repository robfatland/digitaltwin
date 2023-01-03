# Hurdles and Opportunities

## Notes beyond the build

This page is something of a catch-all. What are the technical challenges here?
Any solutions? What further opportunities might we imagine?


- Build in stages, testing each as you go.
    - The sensor / microcomputer / IDE is a good place to start
    - Add the cellular modem; complete the link to the communication service (Twilio)
    - Forward messages to the cloud (AWS: API Gateway / Lambda trigger)
    - Add the cloud storage mechanism (AWS: DynamoDB NoSQL)
    - Return messages to the sensor from the cloud
    - Initiate messages to the sensor from the cloud
    
    
- Expansion from this basic construct
    - Respond to a strong signal from the sensor with a directive
        - 'Change the sampling rate'
    - Extend the build to include multiple clouds
    - Create an API for programmatic access to the digital twin system
    - Timing tests to characterize latency
    

## Alternatives: IOT sensor microcomputer


- Consider Arduino UNO in relation to Raspberry Pi
    - Arduino UNO ATMEGA328P microcontroller is a modest CPU
        - User-friendly IDE, C dialect, maker community
        - Extensive catalog of libraries and accessories
        - 20+ I/O (sensor/control) connections available
            - Digital (incl I2C) and ADC
        - $45 kit includes some sundry development hardware
        - Cellular modem: DFROBOT SUPERSIM 7000
        - Would need a second shield to add SD card storage capacity
        - No native operating system
    - Raspberry Pi 4 Model B
        - 2 GB RAM costs $45
        - Cellular modems are available
        

## Prototype


* Wiring up a prototype
    * How to configure and use a breadboard for external components?
    * How to convert ambient light to a data value? Desktop vibrations?
    * How to control a servomotor? A display screen? An indicator LED? 
    * How to attach and configure a cellular modem add-on board (a 'shield')?
* Setting up a registered SIM card and a method of paying for data transfer
    * We are using twilio for this service
    * Data originates in the leaf endpoint (Arduino system)
    * Cellular modem connects to the cellular network; sends short messages
    * Messages use SIM card authentication
* Establish a leaf endpoint loaded up with programmable communication tools
    * Reiterating: This solution uses services from [twilio](https://twilio.com)
        
        
## JSON, YAML legibility


- Large block of difficult-to-read text
    - JSON? Search 'json beautify': Just like "English -> German" translator
    - Not JSON? Perhaps YAML? Good news: Search 'yaml beautify' likewise


## AT message: Early termination relative to predicted message length


- We would like to terminate a message payload shorter than intended
    - ctrl-z 0x1a can not be sent via the serial monitor to the Arduino
    - Some other seldom-used character such as backtick *can* be sent
    - Have the Arduino translate this character to the ctrl-z stop character
 
 
 ## Outside the cellular networks
 
 
 * Suppose we wish to place sensors beyond the range of any cellular network
     * Iridium satellite modem 
         - Runs $250 to $500
         - [Example product](https://www.adafruit.com/product/4521?gclid=CjwKCAiAheacBhB8EiwAItVO21Se42TcwPgNURyrT8hS_k84LQ4AArFIll1n0TPMxP1NxonIR_NJnBoCpYwQAvD_BwE)
         - Subscription rate $15/month
         - Arduino is one option (postings online circa 2019)
         - Example project: Look up microSWIFT in relation to SWIFT (UW APL)
