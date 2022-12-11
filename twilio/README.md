# twilio 

This section describes the twilio message broker. Twilio is one of many possible
third-party vendors offering a message brokerage service that piggybacks on the cellular
network. What this means in simpler language is roughly this:


### This narrative is too labored so simplify it


- Cell phones communicate via a cellular network
    - This is a conglomeration of telecommunications hardware and software
    - The cellular network infrastructure is built out by service providers
        - Example service providers include AT&T, Verizon, and T-Mobile
        - For this project: We are never aware of which service providers are involved
    - Cell towers transmit and receive messages: 'message passing'
        - Received messages are routed to intended destinations
    - Most message-passing is cell phone to internet or cell phone to cell phone
        - Most of this message-passing in turn uses relatively high bandwidth
- The Arduino uses its SIM7000 cellular modem to connect to the cellular network
    - Arduino messages are very small in terms of data volume
    - The Arduino authenticates itself by means of a preregistered SIM card 
- Twilio operates an internal virtual network complete with its own ip address space
    - This network has capacity for devices like the Arduino prototype
- An Arduino uses a cellular modem with an onboard SIM card to contact the cellular network
