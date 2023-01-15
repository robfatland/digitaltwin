# Introduction

This demonstrator uses an Arduino UNO as the basis of the sensor platform. It use an DFRobot SIM7000 as a
cellular modem; and this is in turn registered with the Twilio networking service. These choices have both
pros and cons, some of which are given below. The bottom line is this: The purpose of this project is to
demonstrate feasibility and complexity to first order. These notes should not be taken beyond that, for
example as a recommended path to take.  


### Arduino UNO / DFRobot SIM7000 / Twilio pros 


- Hardware components for one sensor system < $100
- Cost of operating three sensors is small; "a few dollars per month"
- Arduino UNO is a favorite choice in the Maker space: Very usable IDE and lots of available knowledge online
- Arduino is programmed in a C/C++ dialect with library support built into the IDE
- Twilio is reliable and requires a light-to-moderate basic learning curve
- Twilio abstracts away the cellular network vendors (Verizon etcetera) from this build process
- Arduino processor clock is about 8MHz; so reasonably fast
- Easy to integrate an analog light sensor (photoresistor) and light source (diode laser or LED)


### Arduino UNO / DFRobot SIM7000 / Twilio cons


- System turn-on / config is rather slow, typically 30 seconds
- Building out the Arduino - cellular modem serial communication protocol is challenging
    - Time to climb the learning curve for basic commands and debugging: A couple of weeks
    - Can the recipe here save time? Yes; but it is far from a magic / quick solution
    - The real effort comes in handling asynchronous serial port communication gracefully
- The Arduino UNO has very limited memory available; program size limited to a few hundred lines
- After programming in Python for a while: Returning to C can be aggravating
- The Arduino + modem needs an independent power supply beyond the USB connection (a wall wart)


# Notes: To organize

```
// digitaltwin.ino prototype reporting / responding for Arduino UNO + DFRobot SIM7000
//   cellular modem. Note 2 serial connections: To cellular modem and to IDE: are 
//   respectively modemSerial and Serial.
// 
// Key to comments
// "// ?"" is open questions; "// !" for to-do list items; "// *" for documentation to preserve
//
// Code sources:
//   ATtest.ino for cellular modem pass-thru from IDE
//   emojiomi code for how to do it right (includes NTP, memory optimization, HMAC hashing, etc)
//   servo_tic_toc.ino for some sensor stuff
// 
// * Some notes to transcribe
// * F() places argument into flash memory rather than program memory: keep if possible
// * authentication via (4 + 16) NTP + HMAC hash of payload: Used SpritzCipher.h and EEPROM.h
// * NA points out that git is a useful way of getting a device to report what version it runs
// * and this was built on git-version.h; very elegant and cf alt definition of Big Data.
// * NTP stuff redacted; time.h
// * Watch the Serial IDE echo: Periodically (every 10 sec or so there is a health check...)











// * "Opportunities" += ntp, readout screen, hmac auth, onboard SD storage
// #include <Servo.h>
// Servo servo;
// int lightSensorPin = A0;
// int analogValue    =  0;
// int greenLedPin    = 10;
// int yellowLedPin   = 11;
// int redLedPin      = 12;
// #define LED 12
// #define LASER 7
// #define SERVO 4
// Wiring info
// Using a breadboard? Establish Gd and Vcc (5V)
// Servo cable triple: Brown, Red, Orange > Gd, Vcc, Ctrl-pin-4
// Laser diode: Can drive with a digital pin out probably; Gd Vcc is ok; can overheat
// LED: Gd -- 100 ohm R (Brn Blk Brn) -- flat LED -- digital out pin e.g. 10, 11, 12
// Photoresistor: Vcc -- CdS -- Pin -- 220 Ohm R (Red Red Brn) -- Gd
//                                  -- A0
// OLED: 4 wires Gd Vcc A5 A4 (ADC pins double as I2C clock and signal)
// void setup() {
    // servo.attach(SERVO);         // pin 4
    // pinMode(SERVO, OUTPUT);   
    // pinMode(LASER, OUTPUT);      // pin 7
    // pinMode(greenLedPin,  OUTPUT);    // pins 10, 11, 12
    // pinMode(yellowLedPin, OUTPUT);
    // pinMode(redLedPin,    OUTPUT);
    // digitalWrite(greenLedPin,  HIGH); delay(1000);
    // digitalWrite(yellowLedPin, HIGH); delay(1000);
    // digitalWrite(redLedPin,    HIGH); 
    // servo.write(10);
    // digitalWrite(LASER, HIGH); 
// }
// void blink(int n){
//     for (int i = 0; i < n; i++){ digitalWrite(LED, HIGH); delay(140); digitalWrite(LED, LOW);  delay(70); }
//     delay(1000); return; }
// void servo_slow_pan(int a, int b){
    // int nJumps = int(abs(a - b));
    // int jump_sign = b > a ? 1 : -1;
    // for (int i = 0; i < nJumps; i++) { servo.write(a + jump_sign * i); delay(500); }
    // servo.write(b); return; }
// void loop() {
    // digitalWrite(redLedPin, LOW);
    // delay(2000);

    // pan the servo to its other station
    // if (laserFlip == 0){ servo_slow_pan(10, 20); }
    // else               { servo_slow_pan(20, 10); }
    // digitalWrite(redLedPin,    LOW);  delay(1000);
    // digitalWrite(redLedPin,    HIGH); delay(1000);
    // digitalWrite(redLedPin,    LOW);
    // analogValue = analogRead(lightSensorPin);
    // delay(x);
// }
//
// * question what is modemSerial.listen() doing?
//   Serial uses Arduino built-in serial hardware (1 of 2); 2nd one is a software serial port
//     which is for modemSerial. Otherwise we have to do active switching (i.e. for more than 2)
//     .listen is a software activation of this switch; so it is (we imagine) unnecessary.
//   question what is modemSerial.flush() doing? (Clearing any buffer via 'send')
//   should explain why Serial > Modem does not need any sort of 'all done'   (/r/n or .flush)
//   also note I de-functioned runSerialCLI() for the sake of flat
```



Temporary code copy/paste: From the "rebuild" phase where the carefully constructed code was
failing mysteriously; so I started over from scratch and built it up a piece at a time. The
code below suffers from missing modem serial messages so it is still a major revision from being
operational. 


```
// to do
// test and parse inbound messages
// set up a response-time counter; i.e. stay in response state for T
// digitalOutput pin
// resolve ? comments, particularly using millis() in place of modulo nth
// reinstate sanity measures like reset_connection()? F()?

char sensor = 'a';

#include <DFRobot_SIM7000.h>

#define CTRL_Z 0x1a
#define MODEM_TX 7
#define MODEM_RX 8
#define IP_GATEWAY "100.64.0.1"
#define LISTEN_BUFFER_LEN 128
#define HEALTH_CHECK_PERIOD 30000

static char        listenBuffer[LISTEN_BUFFER_LEN];
SoftwareSerial     modemSerial(MODEM_RX, MODEM_TX);
DFRobot_SIM7000    sim7000(&modemSerial);
static int         lightSensorPin = A0;
static int         lightTransmitPin = 10;
static long int    loop_counter = 0;
static int         nth = 5;
bool               runHealthCheck;
static char        writePtr = 0;
unsigned long      lastHealthCheck = 0;           // look at millis; use in place of nth also?
char               msg[32];
char               msg1[128];

/////////////////////////////////////////
// Utility functions
//
// modem_script() is delivered a sequence of commands, a purpose and an intra-cmd pause time.
//   It's job is to attempt to send this sequence to the cellular modem; and perhaps
//   respond gracefully should things not go to plan. 
//   Note modem_script() is not used to send (data) messages; see sendUDP().
bool modem_script(const __FlashStringHelper *script, char *purpose, uint16_t cmd_delay = 1000) {

    Serial.println("modem_script() running, purpose:");
    Serial.println(purpose);
    Serial.flush();

    char buffer1[100];
    char buffer2[50];
    bool result = true;
    if (!sim7000.checkSIMStatus()) { 
      Serial.println("sim7000.checkSimStatus() fail");
      Serial.flush();
      return false; 
    }
    Serial.println("sim7000.checkSimStatus() succeed");
    Serial.flush();

    // ? define PGM_P and strchr_P for us here: Arduino-specific program memory (ROM?)
    const char *command = (PGM_P) script;
    const char *response = strchr_P(command, '\0') + 1;

    // diagnostic    
    int nCmdsRun = 0;
    char cmdStatus[64];

    while (true) {
        // Serial.println("while: top");
        // Serial.flush();

        char cmd0 = pgm_read_byte(command);
        char rsp0 = pgm_read_byte(response);
        if (cmd0 == '\0' && rsp0 == '\0') { 
            Serial.println("modem script break on 0 0");
            Serial.flush();
            break; 
        }

        char i;
        memset(buffer1, 0, 100);
        memset(buffer2, 0, 50);

        // CEREG checks cellular connection
        if (cmd0 == '\x01' && rsp0 == '\x01') {
            Serial.println("modem script to run CEREG");
            Serial.flush();

            // Wait for network connection
            sim7000.sendCmd("AT+CEREG?\r\n");
            sim7000.readBuffer(buffer1, 100, 1000);
            Serial.print("CEREG loop...");
            // There are two possible good responses to CEREG?: 0,1 and 0,5
            // * potential forever-hang
            if (strstr(buffer1, "+CEREG: 0,1") == NULL && 
                strstr(buffer1, "+CEREG: 0,5") == NULL) { delay(1000); continue; }
        } else {
            for (i = 0; i < 3; i++) {
                strcpy_P(buffer1, (PGM_P)command);
                Serial.println(buffer1);
                if (response[0]) {
                    strcpy_P(buffer2, (PGM_P)response);
                    result = sim7000.checkSendCmd(buffer1, buffer2);
                    if (!result) { delay(300); continue; }
                } else {
                    sim7000.sendCmd(buffer1);
                    sim7000.readBuffer(buffer1, 100, 1000);
                }
                break;
            }
            if (i == 3) { return false; }
            delay(cmd_delay);
        }
        command = strchr_P((PGM_P)response, '\0') + 1; // ratchet to next command
        response = strchr_P((PGM_P)command, '\0') + 1;
        
        nCmdsRun += 1;
        // sprintf(cmdStatus, "end of while, count %d, last: %s", nCmdsRun, buffer1);
        // Serial.println(cmdStatus);
        Serial.flush();
    }
    return true;
}

bool modem_init() {
    const __FlashStringHelper *INIT_CMDS = F(
        "ATE0\r\n\0" "OK\0" // Echo off
        "AT+CMEE=2\r\n\0" "OK\0"
        "AT+CMNB=1\r\n\0" "OK\0"
        "AT+CNMP=38\r\n\0" "OK\0"
        "AT+CIPMUX=1\r\n\0" "OK\0"
        // STATE: IP INITIAL
        "AT+CGDCONT=1,\"IP\",\"super\"\r\n\0" "OK\0"
        "AT+CSTT=\"super\",\"\",\"\"\r\n\0" "OK\0"
        "\x01\0" "\x01\0" // AT+CEREG? -> +CEREG: 0,5 or +CEREG: 0,1
        // STATE: IP START
        "AT+CIICR\r\n\0" "OK\0"
        // STATE: IP GPRSACT
        "AT+CIFSR\r\n\0" "\0"
        // STATE: IP STATUS
        "AT+CLPORT=0,\"UDP\",6969\r\n\0" "OK\0"
        "AT+CIPSTART=0,\"UDP\",\"" IP_GATEWAY "\",\"6969\"\r\n\0" "OK\0"
        // STATE: CONNECT OK
        "AT+CIPHEAD=1\r\n\0" "OK\0" // Identify data packets and their length
        "\0" "\0"
    );
    int msReturn = modem_script(INIT_CMDS, "modem init");
    Serial.println("modem_script() done");
    Serial.flush();
    return msReturn;
}

bool confirmOpenConnection() {
    char buffer[128];
    const char *cmd = "AT+CIPSTATUS=0\r\n";
    memset(buffer, 0, 64);
    sim7000.sendCmd(cmd);
    sim7000.readBuffer(buffer, 128, 1000);
    Serial.print("Connection state: ");
    Serial.println(buffer);
    return strstr(buffer, "\"CONNECTED\"") != NULL;
}

// sendUDP() reports a payload string up to the sky
bool sendUDP(const char *payload, size_t len, char *remark) {
    char lenstring[64];
    sprintf(lenstring, "%d", len);
    // Serial.println("In sendUDP(); remark is:");
    // Serial.println(remark);
    // Serial.println(payload);
    // Serial.println(lenstring);
    // Serial.flush();

    bool result = false;
    char buffer[64];
    char *cursor = buffer;

    String cmd(F("AT+CIPSEND=0,"));        // * 0 is the socket number or connection number
    cmd.concat(len);                       // * NA prototype sends more: A command char, the payload, 4 bytes of 
                                           //     timestamp and 16 bytes of hmac hash-auth data
    cmd.concat("\r\n");                    // * end an AT command
    sim7000.sendCmd(cmd.c_str());
    modemSerial.flush();
    memset(buffer, 0, 64);                         // * Needs documentation... start with 'zero "buffer"'
                                                   // * Flush modem > Arduino msg buffer
                                                   // *   (in case 'this just in!')
    sim7000.readBuffer(buffer, 64, 1000);          // * reads at most 64 bytes; timeout 1 sec
    if (strstr(buffer, ">") != NULL) {             // * strstr() finds string anywhere within string.
                                                   // *   From modem > Arduino the '>' is the prompt for data
                                                   // *   This can fail!                    
                                                   // * former life: msg began with a single-char command
                                                   // * 'buffer' accumulates the payload for use in the hmac hash
        for (size_t idx = 0; idx < len; idx++) {   // * len is payload length
          modemSerial.write(payload[idx]);
        }

                                                              // * Document the thinking here
        modemSerial.write(CTRL_Z);                            //     behaves as EOM
                                                              //     technically not req'd
        modemSerial.flush();                                  // this is a directive to the 
                                                              //   Arduino to send to cellmodem
        memset(buffer, 0, 64);                                // 
        sim7000.readBuffer(buffer, 64, 1000);                 // ...as before
        // Serial.print(buffer);                               // ...so no linefeed appended
        result = strstr(buffer, "SEND OK") != NULL;           // bool for succeed / fail
    }

    // * Document the code below as a safety measure, having sent a UDP msg
    //     Let's make sure we have left data entry mode (which would not recognize
    //     a subsequent AT command): That is, a hung state. Method: Send AT with no
    //     args, should get "OK" back. If so: Good to go. If not: Repeatedly send 
    //     ctrl-z + flush().
    while (true) {
        if (sim7000.checkSendCmd("AT\r\n", "OK")) { break; }
        modemSerial.write(CTRL_Z); // ctrl+z
        modemSerial.flush();
        delay(300);
    }
    return result;
}

void setup(void) { 
    delay(5000);
    Serial.begin(9600);
    Serial.println("setup() Serial.begin() ok; wait about 10 seconds for modem now");
    modemSerial.begin(19200);
    if (!sim7000.turnON()) { Serial.println("modem power on fail"); }
    Serial.println("  sim7000.turnON() done");
    while (true) {
        if (sim7000.setBaudRate(19200)) { break; } // avoid UASH
        delay(1000);
    }
    Serial.println("baud 19200 ok; connecting to network.......\n");
    if (modem_init()) {
        Serial.println("modem_init() ok");
        Serial.flush();
        delay(500);
    }
    sprintf(msg, "%c:awake", sensor);
    int wake_return = sendUDP(msg, 7, "sensor announces self awake");
    if (!wake_return){ Serial.println("sendUDP(wake) returned false..."); } 
    Serial.println("setup() end");
    Serial.flush();
}

void loop() {
    loop_counter += 1;
    sprintf(msg1, "loop() %d", loop_counter);
    Serial.println(msg1);
      
    // health check goes here: if (!confirmOpenConnection()) {

    writePtr = 0;
    modemSerial.listen();
    delay(1000);
    if (modemSerial.available()) {
        while (modemSerial.available()) {   // modem has at least one char for the Arduino           
            char in = modemSerial.read();   // get the next char
            Serial.write(in);
            listenBuffer[writePtr++] = in;
            // if (writePtr == 1) {
            //     Serial.println("started a modem serial read now..............................");
            //     Serial.flush();
            // }
        }
        listenBuffer[writePtr] = '\0';
        modemSerial.flush();                 // ? necessary? placement? hurts the cause???
        Serial.flush();
        Serial.println('That was a modem inbound message:');
        // this is where parse and respond code goes
    }
    else {
        // at this point a debug version would look at Serial.available() with ctrl-z substitution;
        //   but to get out of manual mode: use ATTest, create testbed sketch if necessary.
        if (!(loop_counter % nth)){                        // other logic can go here
            int analogValue = analogRead(lightSensorPin);
            char datastring[64];
            sprintf(datastring, "%d", analogValue);
            sprintf(msg, "%c:data:", sensor);
            strcat(msg, datastring);
            int paylen = strlen(msg);
            Serial.println("loop() broadcast CdS value");
            sprintf(msg1, "%s has length %d and time is %d", msg, paylen, millis());
            Serial.println(msg1);          
            Serial.flush();
            // sendUDP(msg, paylen, "loop sensor data");
        }
    }
}
```
