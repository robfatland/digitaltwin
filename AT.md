# AT cellular modem control

This document describes normal modes of communication with the SIM7000 cellular modem.



```
AT
```
should produce **`OK`**.

```
AT+CIPSTATUS
```
should produce **`OK   STATE: IP INITIAL`** and some time (a minute) later: **`+CPIN: READY    SMS Ready`**.
