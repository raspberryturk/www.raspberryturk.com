---
title: Controlling an AX-12 Dynamixel Servo with Python on a Raspberry Pi
layout: page
image: arbotixmwide.jpg
image_alt: Abstract image of Arbotic-M Robocontroller.
next_url: /details/arm/3dprint.html
next_topic: using Actobotics and Dyanmixels together
---

# Introduction
---

The arm of the Raspberry Turk is powered by two Dyanmixel servos. The servos are controlled by Python running on a Raspberry Pi. This page is a brief guide on how to the servos are setup.

# Components
---

1. [Raspberry Pi](http://www.microcenter.com/product/460968/Raspberry_Pi_3_Model_B)
2. [Arbotix-M Robocontroller](http://www.trossenrobotics.com/p/arbotix-robot-controller.aspx)
3. [FTDI USB to Serial Cable](http://www.trossenrobotics.com/store/p/6406-FTDI-Cable-5V.aspx)
4. [Dynamixel AX-12A Robot Actuator & Included 3 Pin Cable](http://www.trossenrobotics.com/dynamixel-ax-12-robot-actuator.aspx)

# Getting Started
---

The first step is to setup the Arbotix-M Robocontroller. The Arduino compatible microcontroller should be flashed with the pypose sketch. Instructions to install the sketch can be found [here](http://vanadiumlabs.github.io/arbotix/#arbotixsetup).

> The "pypose" sketch provides a pass through from the ArbotiX to the Bioloid bus, by speaking the correct protocol with PyPose.

The PyPose project is capable of a whole lot more than just passing messages through the Arobotix-M, it provides an entire framework for capturing poses and saving them, plus a whole lot more. However, because of the dynamic and simple nature of the SCARA arm on the Raspberry Turk, this was not needed. Once the pypose sketch is installed on the microcontroller it will act as a pass through so the Raspberry Pi can communicate directly with the servo(s).

<center>
	{% include image name="raspberrypifull.jpg" alt="Close up of Raspberry Pi." width="20%" %}
	{% include arrow %}
	{% include image name="arbotixm.jpg" alt="Close up of the Arbotix-M robocontroller." width="20%" %}
	{% include arrow %}
	{% include image name="ax12mid.jpg" alt="AX-12A Dynamixel Servo attached to the arm." width="20%" %}
</center>
{% include caption txt="The Raspberry Pi is connected to the Arbotix-M, which is connected to the AX-12A." %}

According to the documentation for the Arbotix-M:
> The ArbotiX-M will work with input voltages from 7-30V, but is most commonly used at 11-12V, as most DYNAMIXELs work safely at this voltage.

I used [this power supply](https://www.amazon.com/gp/product/B0194B7XT6/ref=oh_aui_detailpage_o00_s00?ie=UTF8&psc=1).

# Dynamixel AX-12A
---

> The AX-12A servo actuator from Robotis is the most advanced actuator on the market in this price range and has become the defacto standard for the next generation of hobby robotics. The AX-12A robot servo has the ability to track its speed, temperature, shaft position, voltage, and load.
>
> [– _Trossen Robotics_](http://www.trossenrobotics.com/dynamixel-ax-12-robot-actuator.aspx)

The ability to get and set the speed and position among other things is one of the reasons I choose the AX-12A for this project. After using it it quickly became one of my favorites to work it.

The servo connects to the Arbotix-M microcontroller via a 3 pin cable. There are several ports for multiple servos on the microcontroller, but you can also daisy chain them because each servo has two 3 pin connectors on the back. Each Dynamixel added to the chain will require it's own ID which can be set before adding it to the chain. Instructions on how to set the ID using the DynaManager software can be found [here](http://learn.trossenrobotics.com/arbotix/1-using-the-tr-dynamixel-servo-tool#&panel1-1). Alternatively it is possible to set the ID directly with setting the ID register using Python as described below.

<center>{% include image name="ax12chain.png" alt="Diagram of chain of Dynamixel servos." %}</center>
{% include caption txt="Dynamixels chained together." %}

# Raspberry Pi
---

Connect the Raspberry Pi to the Arbotix-M via the FTDI USB to serial cable. Find out the serial port of the Arbotix-M using `lsdev` or `ls /dev/{tty,cu}.*`. On the Raspberry Turk, the port is `/dev/ttyUSB0`.

The PyPose project provides a driver which effectively allows you to get and set registers on any connected Dynamixel device. This file was minorly adapted and added to the Raspberry Turk codebase, it can be found [here](https://github.com/joeymeyer/raspberryturk/blob/master/raspberryturk/embedded/motion/pypose/driver.py). Keep in mind this driver has [pySerial](https://pythonhosted.org/pyserial/) as a dependency.

From this point, you can pass in the port and create a `Driver` object to control the servos.

```python
from pypose.driver import Driver

driver = Driver(port='/dev/ttyUSB0')

# Import AX-12 register constants
from pypose.ax12 import P_MOVING, P_GOAL_SPEED_L

# Get "moving" register for servo with ID 1
is_moving = driver.getReg(1, P_MOVING, 1)

# Set the "moving speed" register for servo with ID 1
speed = 888 # A number between 0 and 1023
self.driver.setReg(1, P_GOAL_SPEED_L, [speed%256, speed>>8])
```

A complete list of registers and documentation on how/when to use them can be found [here](http://support.robotis.com/en/product/actuator/dynamixel/ax_series/dxl_ax_actuator.htm). The associated file with all of the register contants is also from the PyPose project, and can be found [here](https://github.com/joeymeyer/raspberryturk/blob/master/raspberryturk/embedded/motion/pypose/ax12.py).
