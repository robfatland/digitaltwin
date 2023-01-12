# Arduino UNO

```
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
