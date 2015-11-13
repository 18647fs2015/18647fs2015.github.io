---
layout: page
title: Setup
permalink: /setup/
---

You have been given a Galileo kit consisting of the following parts:

* The Galileo Gen 2 board and a power plug, the full specifications can be found on the [Intel page](http://www.intel.com/content/www/us/en/embedded/products/galileo/galileo-overview.html).
* A blue case frame to shield the bottom (top case is handed out upon request).
* Wi-Fi card and two internal antennas (mounted at the bottom of the case).
* An Intel Grove kit[^1], that includes sensors, actuators, a 6-pin to USB cable, a micro SD card and adapter.

[^1]: Named in honor of Intel's founder, Andy Grove.

Please take good care of these materials -- you will need it throughout the semester and you will need to return it in good condition at the end of the semester.

You also have access to the Connected Embedded Systems Lab at the back of the CMIL lab.  This lab contains export controlled materials. This means that you should not remove anything from the lab. You are free to take your Galileo and Grove kits out of the lab, however. Please keep them in a secure location.

# Getting Started

The following instructions can also be found on the [official Intel page](https://software.intel.com/en-us/getting-started-with-the-intel-xdk-iot-edition), but are distilled here for better understanding. This guide will help you:

1. Power up your board and connect it to your computer.
2. Login to the board via Serial connection.
3. Create a bootable SD image.
4. Connect the board to the WiFi network.
5. Install and run the Intel XDK IoT edition.

## Power up and connect the Galileo Board

Plug the DC power supply into the wall and the Intel Galileo board.

![Plug in Power Supply](/assets/plug-power-supply.png)

Wait for two green LEDs to light up: USB and ON.

![USB and Power Lights](/assets/power-up-lights.png)

Plug in the 6 pin Serial (FTDI) to USB cable. You find this cable in your Grove kit. Make sure the green cable is facing the side of the board while the black cable is facing the most inwards.

![FTDI Cable Orientation](/assets/ftdi-orientation.png)

Plug the other end into your computer.

![USB Cable](/assets/plug-usb.png)

## Connect to the board via Serial interface

Next you need to connect to the board via its serial interface. The steps differ slightly depending on your operating system.

### Windows

Download the [driver](http://www.ftdichip.com/Drivers/CDM/CDM%20v2.12.00%20WHQL%20Certified.exe).

Run the installer as administrator and install the driver.

![Install Driver](/assets/install-driver.png)

You should see a new **USB Serial Port** entry in the **Ports (COM & LPT)** section of your device manager.

![Install Driver](/assets/install-driver-2.png)

Use a terminal client (e.g. [PuTTY](http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html) or [Xshell](https://www.netsarang.com/products/xsh_overview.html), both are free for educational use) and connect to the board with the following settings:

**Connection type:** Serial  
**Serial line:** COM{X}
**Speed/Baud rate:** 115200  

<span style="width: 45%; display: inline-block;">
![Xshell](/assets/xshell.png)
</span>
<span style="width: 45%; display: inline-block;">
![Putty](/assets/putty.png)
</span>

When you see a blank screen, press the Enter key twice. You should see a login screen. The password is `root`.

### OS X / Linux

Open a terminal.

Execute ls `/dev/tty.*` (note the .\*at the end) command to list all connected devices.

![Result on UNIX](/assets/serial-unix.png)

Look for a device that contains `cu.usbserial`, `tty.usbserial` or `/dev/ttyUSB0`. In this case, the device found was `/dev/tty.usbserial-A402YSYU`.

Connect to the USB serial device using the Terminal screen utility by typing:

    sudo screen /dev/xx.usbserial-XXXXXXXX 115200 –L

Where `/dev/xx.usbserial-XXXXXXXX` is replaced by your device’s unique name.

When you see a blank screen, press the Enter key twice. At the login, use root as a password.

## Verify setup by setting the correct date

Ensure that the date and time is set correctly:

    date -s "YYYY-MM-DD HH:MM:SS"

## Create a bootable Micro SD

The steps to create a bootable Micro SD card are easy but differ from operating system to operating system. Follow the steps outlined in the [Intel Guide](https://software.intel.com/en-us/creating-bootable-micro-sd-card-for-intel-galileo-board).

Verify the installation with the following command:

    cat /etc/version

This should return the following value: `201506210418`

## Obtain the IP address of your board

The connection with the Intel XDK occurs through SSH and thus you need to connect your board to the network and obtain the IP address. Your board comes with a WiFi card. So we will connect with the wireless network. The board also has a ethernet port and you should be able to use this as well if you need a faster connection.

First make sure the WiFi card is properly connected and working:

    lspci -k | grep -A 3 -i "network"

This should return something like this:

    01:00.0 Network controller: Intel Corporation Centrino Advanced-N 6235 (rev 19)
      Subsystem: Intel Corporation Centrino Advanced-N 6235 AGN
      Kernel driver in use: iwlwifi
      Kernel modules: iwlwifi

If that is not the case, please contact the TA of the course.

The IoTDK contains the embedded connection manager connman, which we will use here to establish the Wi-Fi connection. To interface with connman, we will use the command connmanctl. Enter the following series of commands to connect to the wireless network.

    root@galileo:# connmanctl
    connmanctl> enable wifi
      Enabled wifi

    connmanctl> scan wifi
      Scan completed for wifi

    connmanctl> services
      CMU-SV wifi_44850002e9c3_434d552d5356_managed_none
      CMU-SV-BLDG-23 wifi_44850002e9c3_434d552d53562d424c44472d3233_
      managed_psk wifi_44850002e9c3_hidden_managed_ieee8021x
      CROSSMobileWifi wifi_44850002e9c3_43524f53534d6f62696c6557696669_managed_psk
      wifi_44850002e9c3_hidden_managed_none

    connmanctl> agent on
      Agent registered

    connmanctl> connect wifi_44850002e9c3_434d552d5356_managed_none
      [...]
      [ 3298.104102] IPv6: ADDRCONF(NETDEV_CHANGE): wlp1s0: link becomes ready

     connmanctl> quit

     root@galileo:# ifconfig
      [...]
      wlp1s0    Link encap:Ethernet  
                HWaddr 44:85:00:02:E9:C3
                inet addr:10.0.17.236  Bcast:10.0.19.255  Mask:255.255.252.0
                UP BROADCAST RUNNING MULTICAST  MTU:1500  Metric:1
                RX packets:2275 errors:0 dropped:224 overruns:0 frame:0
                TX packets:181 errors:0 dropped:0 overruns:0 carrier:0
                collisions:0 txqueuelen:1000
                RX bytes:330783 (323.0 KiB)  TX bytes:33786 (32.9 KiB)

## Install the Intel XDK IoT Edition

You are free to use any text editor or IDE for the course but we encourage the use of Intel's XDK IoT Edition. It allows connecting to the board direct in the IDE and upload node.js applications into the correct folder. It even allows remote debugging.

[Download the IDE](https://software.intel.com/en-us/iot/downloads#ide).

Run the installer.

Launch the IDE. It will ask you for a Intel login. This is not required and you can skip this step if you wish.

From the Projects tab click on Start a new Project.

![Projects in the XDK](/assets/xdk-projects.png){:width="300px"}

From the left menu, select **Templates**.

![Start a new project](/assets/xdk-new-project.png){:width="300px"}

In the list of templates that displays, select **Onboard LED Blink**, then click Next.

![Templates in the XDK](/assets/xdk-templates.png){:width="300px"}

In the Project Name field, type a name for your project.

![Start a new project](/assets/xdk-new-project-form.png){:width="300px"}

Click Create.

If you are using Windows and have not installed Bonjour a message will pop up. Bonjour allows auto discovery via DNS service to connect to your board in the network. You can install it or skip this step and connect to the board manually (see next steps).

## Connect your board

1. From the IoT Device drop-down list, select Add Manual Connection.
2. In the dialog box that opens, provide the connection information for your development board, as follows:
  * Type the IP address in the Address field.
  * Type the port number in the Port field. The default is 58888.
3. Type your user name and password in the User Name and Password fields.
4. Click **Connect**. A confirmation message is displayed.

## Run the application

This section describes how to run your sample application. By the end of the section, you should see the LED on your development board blink on and off.

1. Click the install/build icon. ![Install/Build Icon](/assets/build.png){:width="20px"}
2. To run your project on the device, click the Run icon.
3. Check your board for a blinking LED.

![Completed Setup](/assets/complete.jpg)

## Final words

Once you got that far you are all set to start working with your kit. Look at the code that is making the LED blink and make yourself familiar with it. There are also many other templates that come with the XDK and you might want to explore some more of them. For example: Can you measure the room temperature?
