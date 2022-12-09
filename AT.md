# AT cellular modem control

This document describes normal modes of communication with the SIM7000 cellular modem.


#### Step 1


On power up:
```
AT
```
should produce 

```
Turn ON SIM7000......
Turn ON !
Set baud rate......
Set baud rate:19200
For example, if you type AT\r\n, OK\r\n will be responsed!
Enter your AT command :
AT

OK

+CPIN: READY

SMS Ready
```

In general 

```
AT
```
produces

```
OK
```

#### Step 2



```
AT+CIPSTATUS
```

Immediate:

```
OK

STATE: IP INITIAL
```

plus subsequently (a minute or less): 

```
+CPIN: READY
SMS Ready
```


### Step 3

Here is the sequence of commands we want to run through to the cellular modem.
These go in one at a time; and I'm using `//` to delimit comments. Unless
otherwise noted, the expected response is **`OK`**.


```
ATE0
AT+CMEE=2
AT+CMNB=1
AT+CNMP=38
AT+CIPMUX=1

// STATE: IP INITIAL (To see status: Issue AT+CIPSTATUS)

AT+CGDCONT=1,"IP","super"
AT+CSTT="super","",""

// Special situation... issue the following command every few seconds.
//   Initially the response will be: +CEREG: 0,3 or possibly 0,0 or 0,2.
//   The response we want is one of these two: +CEREG: 0,5 or +CEREG: 0,1.

AT+CEREG?

// STATE: IP START

AT+CIICR

// STATE: IP GPRSACT

AT+CIFSR
      
// STATE: IP STATUS

AT+CLPORT=0,"UDP",6969
AT+CIPSTART=0,"UDP","" IP_GATEWAY "","6969"
      
// STATE: CONNECT OK

AT+CIPHEAD=1              // Identify data packets and their length
```


