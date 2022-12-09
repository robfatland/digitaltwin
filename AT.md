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



