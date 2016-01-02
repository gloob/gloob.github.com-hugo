---
title: Burning USBaspLoader to Atmega328p using Arduino.
Description: Burning USBaspLoader to Atmega328p using Arduino.
date: 2015-11-14
draft: true
Keywords:
- Development
- Community
- Self
- DIY
- Arduino
- Electronics
Tags:
- Development
- Community
- Self
- DIY
- Arduino
- Electronics
Topics:
- Arduino
- Electronics
- DIY
- Self
---

![](/media/electronics.png "Electronics")

Ok, so you want to build your own microcontroller system in a breadboard or a perfboard and use USBaspLoader bootloader for seamless arduino-like system.

First we need to build up our circuit, the best resource for starting is this Arduino article about [how to build an Arduino system on a breadboard.](https://www.arduino.cc/en/Tutorial/ArduinoToBreadboard)

I build one by myself because at the moment I haven't any other programmer at hand so I can experiment with the bare atmega328p I just received.

First, let's try to burn the Arduino stock bootloader into a pristine atmega328p.

You will need:

1. A breadboard
2. Some capacitors (22pf and 0,1uf)
3. An atmega328p
4. Crystal (not strictly necessary)

Just follow the Arduino article to build the ISP programmer, connect to Arduino (in my case an old Duemilanove), setup the usb connection and board and click on Burn Bootloader option.

In my case, the first time I face this error:

```
Arduino: 1.6.5 (Mac OS X), Board: "Arduino Duemilanove or Diecimila, ATmega328"

/Applications/Arduino.app/Contents/Java/hardware/tools/avr/bin/avrdude -C/Applications/Arduino.app/Contents/Java/hardware/tools/avr/etc/avrdude.conf -v -patmega328p -cstk500v1 -P/dev/cu.usbserial-A9007Qu3 -b19200 -e -Ulock:w:0x3F:m -Uefuse:w:0x05:m -Uhfuse:w:0xDA:m -Ulfuse:w:0xFF:m 

avrdude: Version 6.0.1, compiled on Apr 14 2015 at 16:30:25
         Copyright (c) 2000-2005 Brian Dean, http://www.bdmicro.com/
         Copyright (c) 2007-2009 Joerg Wunsch

         System wide configuration file is "/Applications/Arduino.app/Contents/Java/hardware/tools/avr/etc/avrdude.conf"
         User configuration file is "/Users/aleiva/.avrduderc"
         User configuration file does not exist or is not a regular file, skipping

         Using Port                    : /dev/cu.usbserial-A9007Qu3
         Using Programmer              : stk500v1
         Overriding Baud Rate          : 19200
avrdude: stk500_getsync() attempt 1 of 10: not in sync: resp=0x15
avrdude: stk500_getsync() attempt 2 of 10: not in sync: resp=0x15
         AVR Part                      : ATmega328P
         Chip Erase delay              : 9000 us
         PAGEL                         : PD7
         BS2                           : PC2
         RESET disposition             : dedicated
         RETRY pulse                   : SCK
         serial program mode           : yes
         parallel program mode         : yes
         Timeout                       : 200
         StabDelay                     : 100
         CmdexeDelay                   : 25
         SyncLoops                     : 32
         ByteDelay                     : 0
         PollIndex                     : 3
         PollValue                     : 0x53
         Memory Detail                 :

                                  Block Poll               Page                       Polled
           Memory Type Mode Delay Size  Indx Paged  Size   Size #Pages MinW  MaxW   ReadBack
           ----------- ---- ----- ----- ---- ------ ------ ---- ------ ----- ----- ---------
           eeprom        65    20     4    0 no       1024    4      0  3600  3600 0xff 0xff
           flash         65     6   128    0 yes     32768  128    256  4500  4500 0xff 0xff
           lfuse          0     0     0    0 no          1    0      0  4500  4500 0x00 0x00
           hfuse          0     0     0    0 no          1    0      0  4500  4500 0x00 0x00
           efuse          0     0     0    0 no          1    0      0  4500  4500 0x00 0x00
           lock           0     0     0    0 no          1    0      0  4500  4500 0x00 0x00
           calibration    0     0     0    0 no          1    0      0     0     0 0x00 0x00
           signature      0     0     0    0 no          3    0      0     0     0 0x00 0x00

         Programmer Type : STK500
         Description     : Atmel STK500 Version 1.x firmware

avrdude: stk500_getparm(): (a) protocol error, expect=0x14, resp=0x14

avrdude: stk500_getparm(): (a) protocol error, expect=0x14, resp=0x02

avrdude: stk500_getparm(): (a) protocol error, expect=0x14, resp=0x10
         Hardware Version: -1552793348
         Firmware Version: 2192336.-1552793348
         Topcard         : STK502
         Vtarget         : 1.8 V
         Varef           : 0.0 V
         Oscillator      : Off
         SCK period      : 0.1 us

avrdude: stk500_initialize(): (b) protocol error, expect=0x10, resp=0x01
avrdude: initialization failed, rc=-1
         Double check connections and try again, or use -F to override
         this check.

avrdude: stk500_disable(): protocol error, expect=0x14, resp=0x10

avrdude done.  Thank you.

Problem uploading to board.  See http://www.arduino.cc/en/Guide/Troubleshooting#upload for suggestions.
```

This was because the infamous auto-reset feature in this board.

From the [documentation of the Duemilanove board](https://www.arduino.cc/en/Main/ArduinoBoardDuemilanove) we can read:

*This setup has other implications. When the Duemilanove is connected to either a computer running Mac OS X or Linux, it resets each time a connection is made to it from software (via USB). For the following half-second or so, the bootloader is running on the Duemilanove.*

http://playground.arduino.cc/Main/DisablingAutoResetOnSerialConnection

Correct output burning Arduino Bootload with the capacitor hack in the RST (reset) line.

```
/Applications/Arduino.app/Contents/Java/hardware/tools/avr/bin/avrdude -C/Applications/Arduino.app/Contents/Java/hardware/tools/avr/etc/avrdude.conf -v -patmega328p -cstk500v1 -P/dev/cu.usbserial-A9007Qu3 -b19200 -e -Ulock:w:0x3F:m -Uefuse:w:0x05:m -Uhfuse:w:0xDA:m -Ulfuse:w:0xFF:m 

avrdude: Version 6.0.1, compiled on Apr 14 2015 at 16:30:25
         Copyright (c) 2000-2005 Brian Dean, http://www.bdmicro.com/
         Copyright (c) 2007-2009 Joerg Wunsch

         System wide configuration file is "/Applications/Arduino.app/Contents/Java/hardware/tools/avr/etc/avrdude.conf"
         User configuration file is "/Users/aleiva/.avrduderc"
         User configuration file does not exist or is not a regular file, skipping

         Using Port                    : /dev/cu.usbserial-A9007Qu3
         Using Programmer              : stk500v1
         Overriding Baud Rate          : 19200
         AVR Part                      : ATmega328P
         Chip Erase delay              : 9000 us
         PAGEL                         : PD7
         BS2                           : PC2
         RESET disposition             : dedicated
         RETRY pulse                   : SCK
         serial program mode           : yes
         parallel program mode         : yes
         Timeout                       : 200
         StabDelay                     : 100
         CmdexeDelay                   : 25
         SyncLoops                     : 32
         ByteDelay                     : 0
         PollIndex                     : 3
         PollValue                     : 0x53
         Memory Detail                 :

                                  Block Poll               Page                       Polled
           Memory Type Mode Delay Size  Indx Paged  Size   Size #Pages MinW  MaxW   ReadBack
           ----------- ---- ----- ----- ---- ------ ------ ---- ------ ----- ----- ---------
           eeprom        65    20     4    0 no       1024    4      0  3600  3600 0xff 0xff
           flash         65     6   128    0 yes     32768  128    256  4500  4500 0xff 0xff
           lfuse          0     0     0    0 no          1    0      0  4500  4500 0x00 0x00
           hfuse          0     0     0    0 no          1    0      0  4500  4500 0x00 0x00
           efuse          0     0     0    0 no          1    0      0  4500  4500 0x00 0x00
           lock           0     0     0    0 no          1    0      0  4500  4500 0x00 0x00
           calibration    0     0     0    0 no          1    0      0     0     0 0x00 0x00
           signature      0     0     0    0 no          3    0      0     0     0 0x00 0x00

         Programmer Type : STK500
         Description     : Atmel STK500 Version 1.x firmware
         Hardware Version: 2
         Firmware Version: 1.18
         Topcard         : Unknown
         Vtarget         : 0.0 V
         Varef           : 0.0 V
         Oscillator      : Off
         SCK period      : 0.1 us

avrdude: AVR device initialized and ready to accept instructions

Reading | ################################################## | 100% 0.07s

avrdude: Device signature = 0x1e950f
avrdude: erasing chip
avrdude: reading input file "0x3F"
avrdude: writing lock (1 bytes):

Writing | ################################################## | 100% 0.02s

avrdude: 1 bytes of lock written
avrdude: verifying lock memory against 0x3F:
avrdude: load data lock data from input file 0x3F:
avrdude: input file 0x3F contains 1 bytes
avrdude: reading on-chip lock data:

Reading | ################################################## | 100% 0.03s

avrdude: verifying ...
avrdude: 1 bytes of lock verified
avrdude: reading input file "0x05"
avrdude: writing efuse (1 bytes):

Writing | ################################################## | 100% 0.02s

avrdude: 1 bytes of efuse written
avrdude: verifying efuse memory against 0x05:
avrdude: load data efuse data from input file 0x05:
avrdude: input file 0x05 contains 1 bytes
avrdude: reading on-chip efuse data:

Reading | ################################################## | 100% 0.02s

avrdude: verifying ...
avrdude: 1 bytes of efuse verified
avrdude: reading input file "0xDA"
avrdude: writing hfuse (1 bytes):

Writing | ################################################## | 100% 0.02s

avrdude: 1 bytes of hfuse written
avrdude: verifying hfuse memory against 0xDA:
avrdude: load data hfuse data from input file 0xDA:
avrdude: input file 0xDA contains 1 bytes
avrdude: reading on-chip hfuse data:

Reading | ################################################## | 100% 0.03s

avrdude: verifying ...
avrdude: 1 bytes of hfuse verified
avrdude: reading input file "0xFF"
avrdude: writing lfuse (1 bytes):

Writing | ################################################## | 100% 0.02s

avrdude: 1 bytes of lfuse written
avrdude: verifying lfuse memory against 0xFF:
avrdude: load data lfuse data from input file 0xFF:
avrdude: input file 0xFF contains 1 bytes
avrdude: reading on-chip lfuse data:

Reading | ################################################## | 100% 0.02s

avrdude: verifying ...
avrdude: 1 bytes of lfuse verified

avrdude done.  Thank you.

/Applications/Arduino.app/Contents/Java/hardware/tools/avr/bin/avrdude -C/Applications/Arduino.app/Contents/Java/hardware/tools/avr/etc/avrdude.conf -v -patmega328p -cstk500v1 -P/dev/cu.usbserial-A9007Qu3 -b19200 -Uflash:w:/Applications/Arduino.app/Contents/Java/hardware/arduino/avr/bootloaders/atmega/ATmegaBOOT_168_atmega328.hex:i -Ulock:w:0x0F:m 

avrdude: Version 6.0.1, compiled on Apr 14 2015 at 16:30:25
         Copyright (c) 2000-2005 Brian Dean, http://www.bdmicro.com/
         Copyright (c) 2007-2009 Joerg Wunsch

         System wide configuration file is "/Applications/Arduino.app/Contents/Java/hardware/tools/avr/etc/avrdude.conf"
         User configuration file is "/Users/aleiva/.avrduderc"
         User configuration file does not exist or is not a regular file, skipping

         Using Port                    : /dev/cu.usbserial-A9007Qu3
         Using Programmer              : stk500v1
         Overriding Baud Rate          : 19200
         AVR Part                      : ATmega328P
         Chip Erase delay              : 9000 us
         PAGEL                         : PD7
         BS2                           : PC2
         RESET disposition             : dedicated
         RETRY pulse                   : SCK
         serial program mode           : yes
         parallel program mode         : yes
         Timeout                       : 200
         StabDelay                     : 100
         CmdexeDelay                   : 25
         SyncLoops                     : 32
         ByteDelay                     : 0
         PollIndex                     : 3
         PollValue                     : 0x53
         Memory Detail                 :

                                  Block Poll               Page                       Polled
           Memory Type Mode Delay Size  Indx Paged  Size   Size #Pages MinW  MaxW   ReadBack
           ----------- ---- ----- ----- ---- ------ ------ ---- ------ ----- ----- ---------
           eeprom        65    20     4    0 no       1024    4      0  3600  3600 0xff 0xff
           flash         65     6   128    0 yes     32768  128    256  4500  4500 0xff 0xff
           lfuse          0     0     0    0 no          1    0      0  4500  4500 0x00 0x00
           hfuse          0     0     0    0 no          1    0      0  4500  4500 0x00 0x00
           efuse          0     0     0    0 no          1    0      0  4500  4500 0x00 0x00
           lock           0     0     0    0 no          1    0      0  4500  4500 0x00 0x00
           calibration    0     0     0    0 no          1    0      0     0     0 0x00 0x00
           signature      0     0     0    0 no          3    0      0     0     0 0x00 0x00

         Programmer Type : STK500
         Description     : Atmel STK500 Version 1.x firmware
         Hardware Version: 2
         Firmware Version: 1.18
         Topcard         : Unknown
         Vtarget         : 0.0 V
         Varef           : 0.0 V
         Oscillator      : Off
         SCK period      : 0.1 us

avrdude: AVR device initialized and ready to accept instructions

Reading | ################################################## | 100% 0.05s

avrdude: Device signature = 0x1e950f
avrdude: NOTE: "flash" memory has been specified, an erase cycle will be performed
         To disable this feature, specify the -D option.
avrdude: erasing chip
avrdude: reading input file "/Applications/Arduino.app/Contents/Java/hardware/arduino/avr/bootloaders/atmega/ATmegaBOOT_168_atmega328.hex"
avrdude: writing flash (32670 bytes):

Writing | ################################################## | 100% 0.00s

avrdude: 32670 bytes of flash written
avrdude: verifying flash memory against /Applications/Arduino.app/Contents/Java/hardware/arduino/avr/bootloaders/atmega/ATmegaBOOT_168_atmega328.hex:
avrdude: load data flash data from input file /Applications/Arduino.app/Contents/Java/hardware/arduino/avr/bootloaders/atmega/ATmegaBOOT_168_atmega328.hex:
avrdude: input file /Applications/Arduino.app/Contents/Java/hardware/arduino/avr/bootloaders/atmega/ATmegaBOOT_168_atmega328.hex contains 32670 bytes
avrdude: reading on-chip flash data:

Reading | ################################################## | 100% 0.00s

avrdude: verifying ...
avrdude: 32670 bytes of flash verified
avrdude: reading input file "0x0F"
avrdude: writing lock (1 bytes):

Writing | ################################################## | 100% 0.05s

avrdude: 1 bytes of lock written
avrdude: verifying lock memory against 0x0F:
avrdude: load data lock data from input file 0x0F:
avrdude: input file 0x0F contains 1 bytes
avrdude: reading on-chip lock data:

Reading | ################################################## | 100% 0.03s

avrdude: verifying ...
avrdude: 1 bytes of lock verified

avrdude done.  Thank you.
```

So we have our shiny atmega328p with the arduino bootloader on it. You can now connect a FTDI module to the breadboard or use an Arduino for programming it and check that everything is ok.

Anyhow our target in this article is to burn the USBaspLoader into the atmega328p using the same technique, so let's see how we can do it using the same circuit we are using for burning the Arduino bootloader.

<iframe width="520" height="293" src="https://www.youtube.com/embed/59R2Q8RaEPM" frameborder="0" allowfullscreen></iframe>

