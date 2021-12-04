---
title: Getting Started
type: docs
rev: r1d0
---

# Getting Started

First time using an FPGA? This guide should cover everything you need to get the example verilog project built and loaded up on the ButterStick.

## Step 1: Toolchain Setup
---

This guide will make use of the Open Source FPGA toolchain. It is possible to build and install these from scratch, but the whole process takes around 30 minutes, and can be a bit tricky if you're just getting started. 

It's advised to use an automated build from [YosysHQ/oss-cad-suite-build](https://github.com/YosysHQ/oss-cad-suite-build)

---

Grab the [latest](https://github.com/YosysHQ/oss-cad-suite-build/releases) build for your platform, extract them somewhere on your PC, and add the `oss-cad-suite\bin` folder to your path:

```
 - MacOS:              export PATH=[path-to-bin]:$PATH
 - Linux:              export PATH=[path-to-bin]:$PATH
 - Windows Powershell: $ENV:PATH = "[path-to-bin];" + $ENV:PATH
 - Windows cmd.exe:    PATH=[path-to-bin];%PATH%
```

Check that they've been correctly installed by running:

```bash
$ yosys -V
Yosys 0.9+2406 (open-tool-forge build) (git sha1 c0bcbe1f, gcc 9.3.0-10ubuntu2 -Os)
$ nextpnr-ecp5 -V
nextpnr-ecp5 -- Next Generation Place and Route (Version nightly-20200707)
```

> Note: The above steps only set the PATH variable for the current terminal session. Depending on your platform there are options to ensure the tools are kept in your path. 

---

On linux based systems you may also need to add a udev rule to enable user access to the usb device, once added you'll need to un-plug and reconnect the ButterStick for it to take effect.

To access the bootloader hold down `btn0` while connecting USB. 
```bash
$ cat /etc/udev/rules.d/10-butterstick-dfu.rules
ATTRS{idVendor}=="1209", ATTRS{idProduct}=="5af1", MODE="0666", GROUP="plugdev", TAG+="uaccess"
```

## Step 2: Verilog Example
---
Download the [example](https://github.com/butterstick-fpga/verilog-examples) repository. We'll build the blink example to test that everything is working correctly.
```
$ git clone https://github.com/butterstick-fpga/verilog-examples butterstick-verilog-examples
$ cd butterstick-verilog-examples/blink
$ make

    ... snip ...

Info: [ 66877,  69425) | 
Info: [ 69425,  71973) | 
Info: [ 71973,  74521) | 
Info: [ 74521,  77069) |*** 
Info: [ 77069,  79617) |** 
Info: [ 79617,  82165) |***** 

Info: Program finished normally.
ecppack --compress --freq 38.8 --input blink_out.config --bit blink.bit
cp -a blink.bit blink.dfu
dfu-suffix -v 1209 -p 5af1 -a blink.dfu
dfu-suffix (dfu-util) 0.9

Copyright 2011-2012 Stefan Schmidt, 2013-2014 Tormod Volden
This program is Free Software and has ABSOLUTELY NO WARRANTY
Please report bugs to http://sourceforge.net/p/dfu-util/tickets/

Suffix successfully added to file
rm blink.json blink.bit blink_out.config
```

**🥳 Congratulations you've successfully compiled gateware for the ECP5 using yosys + nextpnr! 🥳**


## Step 3: Verilog Example
---

Next, while holding the button on the ButterStick, plug it in. This enters the bootloader and enables you to upload new gateware.
The LED should be smoothly pulsing to show the device is in bootloader mode.

```
$ make dfu
dfu-util --alt 0 --download blink.dfu --reset
dfu-util 0.9

Copyright 2005-2009 Weston Schmidt, Harald Welte and OpenMoko Inc.
Copyright 2010-2016 Tormod Volden and Stefan Schmidt
This program is Free Software and has ABSOLUTELY NO WARRANTY
Please report bugs to http://sourceforge.net/p/dfu-util/tickets/

Match vendor ID from file: 1209
Match product ID from file: 5af1
Opening DFU capable USB device...
ID 1209:5af1
Run-time device DFU version 0101
Claiming USB DFU Interface...
Setting Alternate Setting #0 ...
Determining device status: state = dfuIDLE, status = 0
dfuIDLE, continuing
DFU mode device DFU version 0101
Device returned transfer size 512
Copying data from PC to DFU device
Download	[=========================] 100%       280788 bytes
Download done.
state(7) = dfuMANIFEST, status(0) = No error condition is present
state(2) = dfuIDLE, status(0) = No error condition is present
Done!
Resetting USB to switch back to runtime mode
```

You should now see the LEDs blinking through colours!

**🥳 Congratulations you've successfully loaded this gateware onto the ECP5 on the ButterStick!**

