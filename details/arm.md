---
title: The Arm
layout: page
image: arm.jpg
next_url: /details/arm/python-dynamixel.html
next_topic: controlling an AX-12A servo with Python on a Raspberry Pi
---

# Introduction
---

The arm is primarily made out of Actobotics components. It is a Selective Compliance Articulated Robot Arm ([SCARA](https://en.wikipedia.org/wiki/SCARA)) which means that it can move freely in the XY-axes remaining fixed in the Z-axis. The motion is controlled by the rotation of two servos which are attached to gears at the base of each link of the arm. At the end of the arm is another servo which moves a beam beam up and down. At the bottom of the beam is an electromagnet that can be dynamically activated to lift the chess pieces. The base of the arm is fixed to the table-top box.

<center>{% include image name="overheadarm.gif" %}</center>
{% include caption txt="The arm capturing a piece." %}

# Structure
---

Actobotics are like really high quality, strong legos for robotics projects, I highly recommend them! The base that is attached to the table-top box is a [Gear Drive Pan Kit](https://www.servocity.com/gear-drive-pan-kit) that was adapted to hold a [Dynamixel Servo](#servos). Because the Dynamixel servo does not use the same spacing as Actobotics, fitting them together was somewhat difficult. I had to 3D print several parts so they would work together nicely. Information on that process can be found [here](/details/arm/3dprint.html). Attached to the top of the gear drive pan kit is a 16.5" aluminum channel with a rotating gear at the end. On the side of the channel is the second servo which rotates the gear. Another piece of channel is attached to the top of that gear. At the end of the arm is a [Gear Rack Kit (Single Parallel)](https://www.servocity.com/785-gear-rack-kit-637169) to control the z-axis motion of the arm.

<center>{% include image name="armdiagram.png" %}</center>
{% include caption txt="The entire arm." %}

# Servos
---

I used [AX-12 Dynamixel Servos](http://www.trossenrobotics.com/dynamixel-ax-12-robot-actuator.aspx) for the core motion of the arm. Dyanmixels fit the job well for several reasons:

1. They are relatively powerful, especially to move the arm as needed
2. Easy read/write to registers via serial interface
3. They can be daisy chained together at no cost
4. 10-bit accuracy for precise movements

\#2 was the main reason for choosing them. The ability to get and set, with 10 bit accuaracy, registers for position and speed was incredibly useful (position/speed are not the only registers though, a full list can be found [here](http://support.robotis.com/en/product/actuator/dynamixel/ax_series/dxl_ax_actuator.htm#Actuator_Address_0C)). The servos are directly powered and controlled by an [Arbotix-M Robocontroller](http://www.trossenrobotics.com/p/arbotix-robot-controller.aspx) but the actual commands came from the Raspberry Pi. The full guide on controlling the servo via Python running on the Raspberry Pi can be found [here](/details/arm/python-dynamixel.html). A detailed notebook regarding the calibration of the arm to move accurately can be found [here](/notebooks/arm_error_correction.html).

<center>{% include image name="ax12.jpg" %}</center>
{% include caption txt="AX-12A Dynamixel Servo." %}

# Gripper
---

The actual gripper mechanism works by activating an electromagnet at the bottom of a vertically moving beam. Each chess piece has a small metal dowel fitted in a drilled hole on the top. More information on how the gripper mechanism works, and the code that runs it can be found [here](/details/arm/gripper.html).

<center>{% include image name="gripper.gif" %}</center>
{% include caption txt="The gripper in motion." %}
