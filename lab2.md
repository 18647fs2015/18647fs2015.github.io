---
layout: page
short_title: Lab 2
title: "Lab 2: Implementing MTP"
permalink: /lab2/
---

| **Assigned** | Thursday, September 17 |
| **Demo Due** | Monday, September 28 |
| **Report Due** | Thursday, October 1 |

**This lab must be completed individually. The groups that have been created are just for sharing of equipment. You are not building a single code base but every member of the team builds its own code base.**

## Objectives

In Lab 2 you will build software that synchronizes your Galileo board with other sources and thus enables your code from Lab 1 to run at externally timed events.

Through this exercise you will learn the following concepts:

* Synchronization on distributed devices.
* Usage of UDP messages in Node.js.
* Writting C/C++ Addons for Node.js.

## Description

After a successful completion of this lab your board will periodically synchronize its internal clock with the clock of other sources. When then receiving a modified MIDI track with absolute timed events, these events are played at the appropriate moment.

The MTP protocol will adjust your local clock to run as close to the golden clock as possible.

## Technology

### Hardware

You will not need additional external hardware components to complete this lab.

### Software

While the technology stack for this lab does not change, the strict time requirements of the software will
most likely force you to write [C/C++ addons for Node.js](https://nodejs.org/api/addons.html).

**Do not use the system time in any way to time your events or synchronize your clock. Do not use any part of the existing NTP.**

## Problem decomposition

You will have to build and interact with the following components.

### Golden Clock

<div style="float: right; margin: 0 0 10px 10px">
<img style="width: 300px; border: 1px solid black;" src="/assets/golden-clock.jpg">
<p><small>The golden clock.</small></p>
</div>
The golden clock is provided to you by the class. It is a Intel Galileo Gen 2 board setup in the lab, connected to the network. It ticks at approximately **10 kHz** and runs as a MTP server on the following endpoint:

[udp://10.0.17.9:12345](udp://10.0.17.9:12345)

The clock is hooked up to a oscilloscope and toggles a GPIO pin every 100 ticks (100 Hz).
This allows you to compare your local clock with the golden clock externally.

**You must also implement such a GPIO as we will assess your clock through the same means during the demo.**

### Local clock

The local clock module is essentially a counter that ticks up on a frequency. The frequency
is in theory up to you, but it makes sense to be a multiple of the golden clock's frequency.

For example, you might not be able to also have 10 KHz counter. But only a 100 Hz counter.
Each tick should then increment the counter by 100 instead of 1 to make up for the difference.

Your clock does not have to implement other common constraints of software clocks, e.g.  gaps and even non-monotonically increases are fine, **but your application, the MIDI player, has to be able to handle the implications.**.

### MTP

MTP stands for Midi Time Protocol. It is a made up name and horrible pun. You don't need to Google it.
But you probably want to read up on NTP.

The protocol defines two messages. All integers are little endian and unsigned.

#### Request message

A message to the server must contain a single unsigned 32-bit integer (t0). This is the
counter of the local clock at the client right before the package was sent.

#### Response message

The server will then respond to the host of the incoming message on the port **12345** with three unsigned 32-bit integers:

1. The first one is the same value as received from the client (t0).
2. The second one is the time of the server clock when received (t1).
3. The third one is the time of the server clock when the message is sent (t2).

### MIDI File Type 3

We will slightly modify the MIDI file structure in the way that the delta value in each message is a absolute value instead of a delay in ticks compared to the previous event. In order for you to detect that it is such a modified file, we will mark the with the file type 3 in the file header.

For example, a NOTE_ON event might now have a delta of 10273. This means that this event should be scheduled
when your clock reaches 10273.

## Testing infrastructure

We provide a testbed for you to verify your implementation. It is accessible on this url:

[http://10.0.13.179:8080/](http://10.0.13.179:8080/)

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
