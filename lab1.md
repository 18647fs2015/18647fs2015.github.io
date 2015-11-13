---
layout: page
short_title: Lab 1
title: "Lab 1: Create a MIDI emitter and receiver"
permalink: /lab1/
---

| **Assigned** | Thursday, September 3 |
| **Demo Due** | Monday, September 14 |
| **Report Due** | Thursday, September 17 |

**This lab must be completed individually. The groups that have been created are just for sharing of equipment. You are not building a single code base but every member of the team builds its own code base.**

# Objectives

In Lab 1 you will build software that turns a Intel Galileo Board into a MIDI event emitter and receiver connected to the cloud.

Through this exercise you will learn the following concepts:

* Reading and writing to analog and digital devices (sensors and actuators).
* Connecting a embedded device over the internet in the role of a client and a server.

# Description

Once you have completed this lab, your Galileo board will have the following functionality: A input device (e.g. rotary angle) is used to select a musical note. This note will be displayed on a display. Once a button is pressed this will fire a MIDI event with the selected note and send it to a central server. The same device will also attempt to register itself at the server on startup and then listen for a incoming TCP connection. On this connection MIDI events can be transmitted and sent to the device which are then interpreted and played on a buzzer.

# Technology

## Hardware

Use your starter kit to implement these functionalities. You will most likely use at least the following components:

* Rotary angle
* Button
* Buzzer
* LED
* LCD screen

## Software

The software technology is JavaScript and Node.js.

![JavaScript](/assets/js.png){:height="100px"}
![Node.js](/assets/node.png){:height="100px"}

Feel free to use any available libraries. Especially useful might be the midi-node module, written for this class:

* [midi-node](https://www.npmjs.com/package/midi-node)

But maybe you want to either extend or modify the library for your needs. We also do not promise the library to be free of bugs.

# MIDI

MIDI (Musical Instrument Digital Interface) is a protocol that allows devices to communicate with each other about audio events and commands.

The MIDI protocol is a binary serial data stream. We will only require a subset of the large feature set of the MIDI protocol but wherever we use the MIDI standard we will be following the specification.

See our summary of the implementation below for more details. You are not required to implement this protocol on your own. You can use the node module we provide.

# Problem decomposition

The full lab can be broken down into multiple steps that are in itself easy to understand and simple to complete.

### Step 1: Add a analog input device and display the result

![](/assets/step1.png){:width="120px"}

Use a analog input device (e.g. rotary angle, but you can use a different one) to select a musical note, like on a Piano. Display the selected note on the LCD screen.

### Step 2: Take a action on the press and release of a button

![](/assets/step2.png){:width="120px"}

Add a button to your setup and change the display when the button is pressed (e.g. a red backlight and change it to something else when the button is released.

### Step 3: Translate the input to a MIDI event

![](/assets/step3.png){:width="200px"}

Take the input and create to MIDI events: NOTE_ON when the button is pressed and NOTE_OFF when the button is released.

### Step 4: Interpret the MIDI events for the buzzer

![](/assets/step4.png){:width="200px"}

Add a buzzer and take the MIDI events from the previous step and play sounds according to the MIDI events.

### Step 5: Send MIDI events through the network

![](/assets/step5.png){:width="475px"}

Connect your device to a central server and send the MIDI event through a TCP connection. Make sure you also send a proper **track header**.

For testing purposes, use our test server. It is available on [10.0.13.179:8080](http:10.0.13.179:8080).

The TCP port is `1337` but make sure you start a test first otherwise the socket will immediately be closed.

### Step 6: Register your device on the server

![](/assets/step6.png){:width="475px"}

On startup, your device should attempt to register at a central server with the following request:

**HTTP Method:** POST  
**Endpoint:** /midi/register  
**Body:** plain-text, UTF-8, address and port of the board  

On success you will receive a status code of 200.

For testing purposes, use our test server. The register request should thus go to `10.0.13.179:8080/midi/register`.

### Step 7: Listen on port for TCP connection

![](/assets/step7.png){:width="475px"}

After the successful POST request, the server will attempt to connect to the address. Make sure your board listens on this port. This will most likely mean the server must be in the same network as the server.

### Step 8: Indicate successful registration

![](/assets/step8.png){:width="475px"}

The board should somehow indicate that it successfully registered and received a connection. Use either a LED light or the display of the LCD screen.

### Step 9: Receive MIDI events as single tracks

![](/assets/step9.png){:width="475px"}

Listen on the open connection for incoming MIDI events. Parse out the relevant events and forward them to the buzzer. Play the events as they come in (ignore the delta value, should be 0 for all events). You should only expect `NOTE_ON`, `NOTE_OFF` and `END_OF_TRACK` messages.

### Step 10: Receive MIDI events as full files

![](/assets/step10.png){:width="475px"}

The server can also send full MIDI files. Here a much broader variety of events can occur. You can ignore most of them. The bigger challenge will be the proper timing of the events as you now have to respect the delta values of events.

## General best practice

### It is no shame to google, it is one not to

Most of the code required for this lab can at least partially be found on the web. Use the resources available to you but indicate larger code parts on your lab report. Most if not all of the code is open source available and you should be able to read through such code bases to solve the problem.

### Test locally

Especially the networking part might be easier to debug locally rather than on the Galileo board. We provide a rudimentary testing infrastructure but you might want to build some parts on your own. Your own TCP server is a question of less then 10 lines of code with Node.js.

### Make reasonable assumptions

This specification may not be complete or ambiguous. Either you clarify your question with us or better yet, you make a reasonable assumption.

### Follow common best practices

We will review your code and this is much more enjoyable if your code is clearly structured and readable. If you build something overly complex think whether it can be done easier. If not at least document it properly.

## Testing infrastructure

We provide a testbed for you to verify your implementation. It is accessible on this url:

[http://10.0.13.179:8080/](http://10.0.13.179:8080/)

The credentials will be emailed to you as soon as you have submitted your GitHub account to the TA.

## Deliverables

At the end of the lab we expect the following deliverables:

### Source Code

Source code demonstrating the functionalities described here.

### Lab report

The lab report has to be submitted in LaTeX following the template provided.

### Live demo

You will schedule a 15 minute demo with the TA that demonstrates the work you have done. Please come with a fully prepared board and running software.

## Resources

* [Node.js API](http://10.0.13.179:8080/)
* [Intel IoT Sensors](http://10.0.13.179:8080/)
* [MIDI messages](http://10.0.13.179:8080/)
