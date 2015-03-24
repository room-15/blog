---
layout: post
title: ESP8266 - Manna of IOT
post_author: fuho
---

[**ESP8266**][esp8266-web] is a wireless
**SOC** (System On a Chip) manufactured by
[Espressif Systems][espressif-web]. It is extremely integrated and
requires *few* external components to run. If used in any of it's modularized
form, number of external component can go down to zero *(that is if you can
figure out how to power it from thin air)* ;)  

The chip comes in a module in many shapes and forms (so far I have counted 12),
but all of them use the same SOC at it's core, you can learn on any of them.
The modules will differ in number of GPIOs (General Purpose Inputs / Outputs),
antennas (PCB trace antenna, ceramic SMT antenna, mini antenna connector, pin or
any combination of the above), flash memory size.

I will be describing [ESP-01](#pinout) (the very first version I got). If you don't have
one yet, you can find them for ~$3 / piece or less on AliExpress or eBay,
I believe that you, the esteemed reader, can find the seller easily yourself.
As of now I only have two ESP-01 modules, but I have a lot more (ESP-12 and
ESP-07) coming my way straight from Shenzen.

## Features
 - [SDIO 1.1/2.0][sdio], [SPI][spi], [UART][uart]
 - 32-pin [QFN][qfn] package
 - Integrated [RF switch][rf-switch], [balun][balun], 24dBm PA, DCXO, and PMU
 - Integrated [RISC][risc] processor, on-chip memory and external memory interfaces
 - Integrated MAC/baseband processors
 - [Quality of Service management][qos]
 - [I2S][i2s] interface for high fidelity audio applications
 - On-chip low-dropout linear regulators for all internal supplies
 - Proprietary spurious-free clock generation architecture
 - Integrated [WEP][wep], [TKIP][tkip], [AES][aes], and [WAPI][wapi] engines

## Specifications
 - 802.11 b/g/n
 - [Wi-Fi Direct (P2P)][wifi-direct], [soft-AP][soft-ap]
 - Integrated [TCP/IP][tcpip] protocol stack
 - Integrated [TR switch][tr-switch], LNA, power amplifier and matching network
 - Integrated PLLs, regulators, DCXO and power management units
 - +19.5dBm output power in 802.11b mode
 - Integrated low power 32-bit CPU could be used as application processor
 - [STBC][stbc], [1×1 MIMO][mimo], [2×1 MIMO][mimo]
 - A-MPDU & A-MSDU aggregation & 0.4ms guard interval
 - Wake up and transmit packets in < 2ms
 - Standby power consumption of < 1.0mW (DTIM3)
 - Power down leakage current of <10uA

**TL;DR;** If this doesn't make much sense to you, don't worry,
it's good, buy it now.

## ESP-01
<a name="pinout"></a>

{%highlight text %}
█▀▀▀▀▀▀▀▀▀▀▀▀▀▀▀▀▀▀▀▀▀▀▀▀▀▀▀▀▀▀▀▀▀▀▀▀▀▀▀▀▀▀▀▀▀▀▀▀▀▀▀▀▀▀▀▀▀▀▀▀▀▀▀▀▀▀▀▀▀▀▀▀▀▀▀▀▀█
█                             ███████       ███████                           █░
█ ▐█▌                                                                         █░
█ ▐█▌                         ███████       ███████           ▄████▄  ▄████▄  █░
█ ▐█▌                                                        ▐█▌07▐█▌▐█▌08▐█▌ █░
█ ▐█▌                         ███████       ███████           ▀████▀  ▀████▀  █░
█ ▐█▌                                                                         █░
█ ▐███████████████▌    ██     ███████       ███████           ▄████▄  ▄████▄  █░
█               ▐█▌    ██                                    ▐█▌05▐█▌▐█▌06▐█▌ █░
█ ▐███████████████▌    ██                                     ▀████▀  ▀████▀  █░
█ ▐█▌                  ██           █▐▌█▐▌█▐▌█▐▌█▐▌█▐▌                        █░
█ ▐███████████████▌    ██        ▀▀  _  __  _         ▀▀      ▄████▄  ▄████▄  █░
█               ▐█▌    ██        ▀▀ |_ (_  |_)        ▀▀     ▐█▌03▐█▌▐█▌04▐█▌ █░
█ ▐███████████████▌    ██        ▀▀ |_ __) |          ▀▀      ▀████▀  ▀████▀  █░
█ ▐█▌                  ██        ▀▀    _  _   _   _   ▀▀                      █░
█ ▐███████████████▌    ██        ▀▀   (_)  ) |_  |_   ▀▀      ▄████▄  ▄████▄  █░
█               ▐█▌    ██        ▀▀   (_) /_ |_) |_)  ▀▀     ▐█▌01▐█▌▐█▌02▐█▌ █░
█ ▐███████████████▌    ██        ▀▀                   ▀▀      ▀████▀  ▀████▀  █░
█ ▐█▌                  ██        ▀▀                   ▀▀                      █░
█ ▐████████████████████████████     █▐▌█▐▌█▐▌█▐▌█▐▌█▐▌                        █░
█                                                                             █░
█▄▄▄▄▄▄▄▄▄▄▄▄▄▄▄▄▄▄▄▄▄▄▄▄▄▄▄▄▄▄▄▄▄▄▄▄▄▄▄▄▄▄▄▄▄▄▄▄▄▄▄▄▄▄▄▄▄▄▄▄▄▄▄▄▄▄▄▄▄▄▄▄▄▄▄▄▄█░
 ░░░░░░░░░░░░░░░░░░░░░░░░░░░░░░░░░░░░░░░░░░░░░░░░░░░░░░░░░░░░░░░░░░░░░░░░░░░░░░░
{% endhighlight %}

Pins have a standard spacing of 2.54mm and as you can see are not very
breadboard friendly (they will short if you just plug it in a standard
breadboards), so you will either have to de-solder the header, use
use female to male jumper wires (you can never have enough of those) or use
testing clips.

### Pins Explained

| **1 - `GND`**  | **3 - `GPIO2`** | **5 - `GPIO0`** | **7 - `URxD`** |
|:--------------:|:---------------:|:---------------:|:--------------:|
| **2 - `UTxD`** | **4 - `CH_PD`** | **6 - `Reset`** | **8 - `Vcc`**  |

 1. Ground
 2. Serial mode TX (Data to be transmitted over the comms link)
 3. General Purpose Input Output 2
 4. Chip select (High = On, Low = Off)
 5. General Purpose Input Output 0
 6. Reset (Pull to Low for Reset)
 7. Serial mode RX (Data that has been received from the comms link)
 8. Power, 3.3 V - DC

### Serial modem mode
When you receive your ESP8266 (in a module form) it will most likely come
pre-configured as a serial modem. This is the simplest mode, hence the default.
You will be sending commands to the modem over serial protocol and it will obey.

### Why should I ever use this mode
If you're like me, you will want to get started with the module as soon as
possible and this is the default and arguably the simplest way of interfacing the
ESP8266.
Or you already use an Arduino and all you want is to connect it to the Interweb.

### Alright! Let's get started then!
You will need a serial port, couple wires and I was going to say a power supply,
(I decided to check if the external 3.3V power is really necessary and it
turns out that my USB-to-Serial adapter's 3.3V regulator supplies enough power
for basic functionality. You will find mixed messages on the Internet, some say
you need an external 3.3 power, some say you don't. I started with a lab power
supply and have now disconnected it. If anything changes I will let you know.)
Two things to take from this, make sure your serial port's Vcc is supplying
3.3V and not 5V and I forgot the other thing.

So it looks like all you need is a USB to RS232 adapter, you might even have
one lying around, or an old PC with serial port or newish ThinkPad ;)
If you don't, eBay is your friend, they can be had for literally less than a
dollar, including delivery.

Alright then, wiring it up should be obvious, let's get to it.

### Wiring
 1. Connect `3.3V` and `Ground` from your USB-to-Serial to the ESP-01.
 2. Cross-connect the serial port, that is serial `TX` to esp `RX`
 and vice versa.
 3. Set `CH-PD` to high (Connect it to to 3.3V)
 4. Now rest after all this work

### Communicating with the module for the first time
Module wired, serial dongle in your hand, plug the serial converter to an empty
USB port. Start a serial console program of your choice (You might already have
Putty if you're on Windows, if you'Re on linux you don't need my help),
find your serial port, set baud rate to 115200baud, 8bit data length,
no parity and 1 stop bit. Connect (if your software allows you to choose a line
end characters choose CR,LF)  

#### Serial settings
 - Baud rate: **115200**
 - Data:     **8bits**
 - Parity: **None**
 - Stop Bit: **1**

**Moment of truth!** Send `AT` and you shall receive `OK`, that was easy!

###Next time!
**We will compile a list of all known AT commands you can use with
the ESP8266 module as it comes configured from factory.` We will connect to
our wireless network as a client, run our WiFi access point and maybe even
send some data from the module to the Internet.**

### PS: One of many advantages of external power supply
Should you still decide to run the module from an external supply, you will be
able to see an interesting dump as soon as the module it powers up. You are
very likely to miss this data if you power it up from the serial adapter.

#### Output on power up (in HEX, it doesn't make any sense otherwise):

{% highlight text %}
    00000000: 72 6c 00 6c 9c 9e 7c 00   8c 6c e0 7c 02 0c 0c 0c
    00000010: 8c 0c 6c ec 0c 62 7c 8e   82 02 ec 12 92 72 92 62
    00000020: 6c 0c 62 8c f2 6e 6e 9e   6c 6e 6e 9c e2 ec 0c 62
    00000030: 1c 70 ec 8e 6c 72 6c 72   6c 70 f2 6e e0 10 02 0c
    00000040: 0c 82 0c 6c 0c 0c 0c 0c   0c 0c 62 0c 6e e2 7c 02
    00000050: 8c 0c 8e 8e 8e 8e 62 8c   f2 6e 6e ee 00 6c 8c 8e
    00000060: 6c 60 02 90 12 12 6e 6e   6c 8c 6c 60 02 0e 02 6e
    00000070: 72 8e 92 92 6e 0c 0c 92   02 6c 60 02 60 f2 6e 0c
    00000080: 0c 0c 9e e0 62 82 6e 6c   8c 0c 8c f2 6e 6e ee 00
    00000090: 0c 8e 0e 6c 70 f2 6e e0   10 02 0c 0c 72 8c 9c 9c
    000000a0: e2 e0 0c 0c 0c 0c 62 0c   6e e2 7c 02 ec 6c 8c 8e
    000000b0: 0c 62 8c f2 6e 6e ee 00   6c 8c 0c 6c 60 02 90 12
    000000c0: 12 6e 6e 0c 8e 0e 6c 0e   02 6e 72 8e 92 92 6e 0c
    000000d0: 0c 12 ec 6c 60 02 70 f2   6e e0 10 02 0c 0c 72 8c
    000000e0: 9c 9c e2 e0 ec e2 0c 0c   62 0c 6e e2 7c 02 0c 8e
    000000f0: 7c 92 8c 8e 62 8c f2 6e   6e ee 00 0c 8e 6c 60 02
    00000100: 90 12 12 6e 6e 0c 6c 60   02 0e 02 6e 72 8e 92 92
    00000110: 6e 0c 0c 62 82 92 60 02   0e 72 92 92 6e 0c 0c 62
    00000120: 82 92 60 02 0d 0a 72 65   61 64 79 0d 0a
{% endhighlight %}

### Links
[esp8266-web]:http://espressif.com/en/products/esp8266/
[espressif-web]:http://espressif.com
[sdio]:http://en.wikipedia.org/wiki/Secure_Digital#SDIO
[spi]:http://en.wikipedia.org/wiki/Serial_Peripheral_Interface_Bus
[uart]:http://en.wikipedia.org/wiki/Universal_asynchronous_receiver/transmitter
[qfn]:http://cds.linear.com/docs/en/packaging/QFN_32_05-08-1693.pdf
[risc]:http://en.wikipedia.org/wiki/Reduced_instruction_set_computing
[qos]:http://en.wikipedia.org/wiki/Quality_of_service
[i2s]:http://en.wikipedia.org/wiki/I%C2%B2S
[wep]:http://en.wikipedia.org/wiki/Wired_Equivalent_Privacy
[tkip]:http://en.wikipedia.org/wiki/Temporal_Key_Integrity_Protocol
[aes]:http://en.wikipedia.org/wiki/Advanced_Encryption_Standard
[wapi]:http://en.wikipedia.org/wiki/WLAN_Authentication_and_Privacy_Infrastructure
[wifi-direct]:http://en.wikipedia.org/wiki/Wi-Fi_Direct
[soft-ap]:http://en.wikipedia.org/wiki/SoftAP
[tcpip]:http://en.wikipedia.org/wiki/Internet_protocol_suite
[rf-switch]:http://en.wikipedia.org/wiki/RF_switch
[tr-switch]:http://en.wikipedia.org/wiki/Duplexer#Transmit-receive_switch
[balun]:http://en.wikipedia.org/wiki/Balun
[stbc]:http://en.wikipedia.org/wiki/Space%E2%80%93time_block_code
[mimo]:http://en.wikipedia.org/wiki/MIMO
