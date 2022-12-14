# Hurdles

What are the technical challenges involved in the 'recipe' given here? This outline may
help evaluate what one is signing up to deal with. Notice that debugging tools 
figure prominently; and in some cases solutions are mentioned.


* Obtaining appropriate leaf-node technology
    * We use the Arduino UNO
        * CPU is lightweight (an ATMEGA328P microcontroller)
        * Supported by a user-friendly IDE
            * Programming language is essentially C
        * 20+ I/O (sensor/control) connections available
            * Includes digital, e.g. I2C devices
            * Including analog-to-digital
        * Uses free supporting libraries for IoT connectivity
        * Cheap: $30 or $45 adds in some ancillary hardware
        * Vast array of add-on "shields" available
            * For our purposes particularly: DFROBOT SUPERSIM 7000 cellular modem
        * Negatives
            * Limited capabilities "unless we add another shield"
                * Limited program memory
                * Limited data storage unless we add an SD card shield
            * No native operating system (no compiler, no Linux utilities etcetera)
            * Limited debugging capabilities
        * Alternative: Raspberry Pi category PC
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
    * Twilio acts as a middle-man in the communication chain
* Establish a cloud endpoint
    * This solution uses services from AWS, Azure and GCP
        * AWS uses an API Gateway coupled to an AWS serverless Lambda function
        * GCP TBD
        * Azure TBD
    * Serverless functions need access to the Python twilio REST API library
        * AWS this means bundling a zip file
* We have a large block of difficult-to-read text
    * Could it be JSON? Good news: Search 'json beautify' which behaves just like a "English -> German" translate website
    * Not JSON? Could it be yaml?? Good news: Search 'yaml beautify' which behaves likewise
       
