# Hurdles and Opportunities

What are the technical challenges involved in the 'recipe' given here? 
What are the non-recipe challenges -- let's call them opportunities -- to
take other pathways to digital twin success?


This outline may is intended to help evaluate what one is signing up to deal with.
The development process is intended to be gradual: One stage at a time, and test
each incremental step as you go. Debugging tools and methods figure prominently; 
and for some obstacles our solutions are indicated. 

The first version of the challenge list is an executive summary. Following this
is a longer version, 'exhausting detail'. Finally: Opportunities.


### Abbreviated list of challenges


* Building a prototype sensor device that can send and receive information
* Setting up the chain of communication to the cloud
* Setting up the cloud endpoint that connects to the prototype device
* Writing data to an accessible storage location
* Debugging each step of a gradualist approach to building this system


The goal here is to connect to more than one cloud as an excercise in validating 
equivalence. 


### Exhaustive list of challenges


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
* Specific catch-points 
    * We have a large block of difficult-to-read text
        * Could it be JSON? Good news: Search 'json beautify' which behaves just like a "English -> German" translate website
        * Not JSON? Could it be yaml?? Good news: Search 'yaml beautify' which behaves likewise
    * We would like to terminate a message payload that is shorter than intended
        * ctrl-z 0x1a can not be sent via the serial monitor to the Arduino
 
 
 ### Opportunities
 
 * Place sensors outside of cellular network coverage
     * Iridium satellite modem runs $250 to $500
         * [Example product](https://www.adafruit.com/product/4521?gclid=CjwKCAiAheacBhB8EiwAItVO21Se42TcwPgNURyrT8hS_k84LQ4AArFIll1n0TPMxP1NxonIR_NJnBoCpYwQAvD_BwE)
     * Subscription rate $15/month
     * Arduino possible (postings online circa 2019) but a raspberry pi might be a stronger option
     * Example project: Look up microSWIFT in relation to SWIFT (UW APL)
