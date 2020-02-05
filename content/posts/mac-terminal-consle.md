---
title: "Mac Terminal Consle"
date: 2020-02-05T00:12:10Z
draft: false
---

Step 1: find usb serial on the /dev


Step 2: use below command 
```bash
screen ${usb serial device on the /dev} ${connected device rate} 
```
※ connected device rate almost 9600 or 115200


Connection to Device
```bash
screen /dev/tty.usbserial-FT2H04WO 9600
```

Disconnect to Device
```
quit or exit on the device 

Press [Ctrl] + A , [Ctrl] + \

```