---
title: Gripper
layout: page
image: electromagnet.jpg
next_url: /details/vision.html
next_topic: how the Raspberry Turk sees the chessboard
---

# Introduction
---

The gripper mechanism is used to pick up and place down chess pieces. It consists of a servo for vertical motion and an electromagnet to hold the pieces.

<center>{% include image name="gripper.gif" %}</center>

# Electromagnet
---

The electromagnet is attached to the bottom on the rod and can be controlled via Python through A GPIO pin connected to the gate of a transistor. The transistor is connected in circuit with a 12V power supply and a [1" diameter electromaget with 19 lbs. holding force](https://apwelectromagnets.com/em100-12-122.html). A detailed spec sheet for the electromagnet can be found [here](https://apwelectromagnets.com/files/attachments/137/5536.pdf). The electromagnet is from APW Company, who sells tons of different kinds and sizes of electromagnets and other custom coils.

<center>{% include image name="electromagnetcircuit.png" %}</center>

The code controlling the electromagnet can be found in the [gripper.py](https://github.com/joeymeyer/raspberryturk/blob/master/raspberryturk/embedded/motion/gripper.py) file in the project. The relevant parts of the file are below.

```python
import RPi.GPIO as GPIO
electromagnet_pin = 40
GPIO.setmode(GPIO.BOARD)
GPIO.setup(electromagnet_pin, GPIO.OUT)

def electromagnet(self, on):
	output = GPIO.HIGH if on else GPIO.LOW
	GPIO.output(electromagnet_pin, output)
	
# Dynamically turn on/off electromagnet
electromagnet(True) # On
# ... move piece to another square ...
electromagnet(False) # Off
```

# Servo
---

The vertical motion, to pick up and drop off pieces, is accomplished with a [gear rack kit](https://www.servocity.com/785-gear-rack-kit-637169) made out of Actobotics from ServoCity. The [Hitec HS-785HB Servo](https://www.servocity.com/hs-785hb-servo) is controlled by the Raspberry Pi via adjusting the PWM signal of a GPIO pin. This rotation moves the aluminum beam that the electromagnet is attached to. Relevant code for controlling the gripper servo in Python is found in [gripper.py](https://github.com/joeymeyer/raspberryturk/blob/master/raspberryturk/embedded/motion/gripper.py).

<center>{% include image name="servocircuit.png" %}</center>

<center>{% include image name="gearrackkit.gif" %}</center>
{% include caption txt="Animation of the gear rack kit moving a piece up/down." %}

# Pieces
---

The chess pieces are a set of plastic [3 3/4" Colored Chess Pieces](http://www.chesshouse.com/3_3_4_Colored_Chess_Pieces_p/e104c.htm?1=1&CartID=0) in neon green and orange. The colors provided enough visual distinction for [computer vision](/details/vision.html) algorithms. The top of each piece was drilled and a small metal dowel, from a local hardware store, was interted. This allowed the electromagnet to lift the pieces.

<center>{% include image name="metalpiecetop.jpg" %}</center>
{% include caption txt="There is a small metal dowel in the top of each piece." %}
