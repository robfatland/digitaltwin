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
should produce **`OK   STATE: IP INITIAL`** and some time (a minute) later: **`+CPIN: READY    SMS Ready`**.
