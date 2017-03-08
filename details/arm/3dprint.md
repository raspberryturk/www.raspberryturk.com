---
title: Using Dynamixel Servos with Actobotics
layout: page
image: dynamixelactobotics.jpg
next_url: /details/arm/gripper.html
next_topic: how the gripper works
---

# Introduction
---

Actobotics are structural and motion components for robotics projects. The components are made out of high grade aluminum or stainless steel and all use standardized sizes, and connect with ease, a lot like legos. I bought all of my Actobotic parts for the Raspberry Turk through [ServoCity](https://www.servocity.com/actobotics), who were incredibly helpful throughout the process when I had questions.

Actobotics work extremely well when used with other Actobotics components, but not always with other products. I ran into this problem when trying to use Dynamixel AX-12A servos with the arm made out of Actobotics. There were two problems:

1) Connecting a pinion gear to the servo
<center>{% include image name="ax12.jpg" width="40%" %}{% include image name="pinion.jpg" width="40%" %}</center>

2) Fixing the servo to the arm so the pinion gear aligns with the arm gear
<center>{% include image name="channel.jpg" width="40%" %}{% include image name="ax12side.jpg" width="40%" %}</center>

I was able to solve both of these problems by 3D printing parts to fit the job.

# Connecting an Acotobtics Pinion Gear to a Dynamixel
---

<center>{% include image name="ax12adiagram.png" width="40%" %}{% include image name="piniondiagram.png" width="40%" %}</center>

Using [Tinkercad](https://www.tinkercad.com/), an easy browser-based 3D design and modeling tool, I was able to design a small plate with a cylinder popping out of it which fit the job.

<center>{% include stl.html name="dynamixel_adapter.html" width="75%" %}</center>
<center>{% include image name="3dservo.gif" %}</center>
{% include caption txt="Visualization of the adapter alone and on the servo." %}

The design is publicly available [here](https://tinkercad.com/things/hqfiNy3yeVC), and can be adapted for your own purposes. I first printed several copies using ABS material at a local makers workshop. This was great for prototyping and gave me a final design. The ABS material fit well but cracked easily under the stress caused by torsion when moving the entire arm. I took the final design and uploaded it to [Shapeways](https://www.shapeways.com) and printed it in stainless steel. You can order it [here](https://www.shapeways.com/product/MWLKVSM23/dynamixel-adapter?optionId=61806374&li=shop-inventory), or modify/print it however you like using the [.STL](/downloads/dynamixel_adapter.stl).

# Attaching AX-12A to Actobotics Aluminum Channel
---

The arm of the robot is made out of [aluminum channel](https://www.servocity.com/structural-components/channel/standard-channel). The sides of the channel have holes patterned in a way that makes mounting other Actobotic parts very easy. However the [side plate](http://www.trossenrobotics.com/bioloid-frame-f3) that comes with the AX-12A has holes of a different size and spacing.

<center>{% include image name="channeldiagram.jpg" width="40%" %}{% include image name="f3.png" width="40%" %}</center>

This created a problem, there is no way to securely attach the servos to the arm using any standard parts. To solve this, for one of the servos I tried forcefully screwing small screws through the holes in the aluminum channel and into the hard engineering plastic of the Dynamixel side plate. This worked well for the servo at the elbow.

<center>{% include image name="ax12sideplatescrews.jpg" %}</center>
{% include caption txt="Screwed through the aluminum channel directly into the AX-12A side plate." %}

However, I couldn't get a tight enough fit when I tried this same technique on the servo at the base of the arm. I decided to go back to the 3D printing to solve this problem. Thankfully, [ServoCity provides STEP files for ALL of the Actobotic components the sell](https://www.servocity.com/step-files). This is just one more reason using Actobotics is such a joy, any component or adapter you need a custom part for, the STEP files are all there for you to work with. Trossen Robotics also provides excellent STEP files for the Dynamixel AX-12A. This allowed me to put the pieces together and build a custom side plate that secured the servo tightly to the aluminum channel. I printed this on Shapeways in the strong & flexible plastic material.

<center>{% include stl.html name="dynamixel_sideplate.html" %}</center>
<center>{% include image name="3darmbase.gif" %}</center>
{% include caption txt="Visualization of the side plate alone and attached the servo/channel." %}

The side plate design is publicly available [here](https://tinkercad.com/things/5F510Eoo51f).
