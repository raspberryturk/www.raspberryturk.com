---
title: Vision
layout: page
image: camera2.jpg
image_alt: Abstract image of the Raspberry Turk camera.
next_url: /details/vision/data_collection.html
next_topic: the data collection process
---

# Introduction
---

The Raspberry Turk uses computer vision to recognize where the chess pieces are on the board before deciding what move to make.

<center>
{% include image name="viewfromturk.jpg" alt="The chessboard from the perspective of the Raspberry Turk's side." width="40%" %}
{% include arrow %}
{% include image name="chessboard4.svg" alt="A chessboard diagram corresponding to the board as seen from the perspective of the Raspberry Turk." width="30%" %}
</center>

# Camera
---

The robot sees through a [Raspberry Pi camera module](https://www.raspberrypi.org/products/camera-module/) attached with an [HDMI cable](https://www.tindie.com/products/freto/pi-camera-hdmi-cable-extension/) to a fixture directly above the chessboard. For more information on how the camera is attached, and how the fixture is setup, click [here](/details/table.html).

<center>{% include image name="camera.jpg" alt="The camera and light fixture above the chessboard." %}</center>

The camera is controlled via Python OpenCV running on the Raspberry Pi. The raw image is converted to a 480x480px image by warping the perspective so the chessboard squares are equal size and the non-chessboard part of the image is cropped. A description of the process for finding the correct perspective transform matrix can be found [here](/details/vision/camera_calibration.html).

<center>
{% include image name="capture.jpg" alt="A raw image captured from the camera." width="40%" %}
{% include arrow %}
{% include image name="chessboardframe.jpg" alt="Chessboard after warping the perspective of the raw image captured by the camera." width="30%" %}
</center>
{% include caption txt="Raw camera capture converted to final product." %}

# Collecting a Dataset
---

In order to build a system that can recognize which pieces are on which squares of a chessboard by analyzing an image we need a large dataset to train and validate a computer vision model. In order to collect a dataset I wrote several scripts and sat for hours moving pieces to capture multiple placements for every piece in every square on the board. More information on how the dataset was collected can be found [here](/details/vision/data_collection.html).

<center>{% include image name="collection.gif" alt="Timelapse of dataset collection." %}</center>
{% include caption txt="Collection of a previous dataset over several hours." %}

Each image is then cut into 64 smaller images and stored with the appropriate label.

<center>
	{% include image name="rawcapture.png" alt="480x480 chessboard captured by camera." width="20%" %}
	{% include arrow %}
	{% include image name="rawcapturesliced.png" alt="The chessboard split into 64 individual squares." width="27%" %}
	{% include arrow %}
	{% include image name="knightf7.jpg" alt="An individual square containing an orange knight." width="13%" %}
</center>
{% include caption txt="Process from raw captured chessboard data to individual labeled squares (orange, knight, f7, etc)." %}

This dataset is publicly [available](https://www.kaggle.com/joeymeyer/datasets) on Kaggle, released under the [(CC BY-SA 4.0) license](https://creativecommons.org/licenses/by-sa/4.0/).

# Vision Model
---

Instead of trying to figure out what type of piece is on each square, we can take a shortcut. Because we know the state of the game, all we need to do is track which piece moved. By only tracking changes we can internally keep track of which pieces are on which squares.

<center>
{% include image name="chessboard1.svg" alt="Chessboard diagram in a certain game state (Kasparov vs. Deep Blue 1997 Game 1)." width="20%" %}
{% include arrow %}
{% include image name="colorboardmaskfade.gif" alt="Animation of colored circles representing what the Raspberry Turk sees, fading to the actual chessboard captured by the camera." width="20%" %}
{% include arrow %}
{% include image name="chessboard2.svg" alt="Updated chessboard diagram after having determined which move the played made." width="20%" %}
</center>
{% include caption txt="By observing which color pieces are in which squares, the robot can determine which move was made." %}

Our goal is to create an algorithm that can determine whether a square contains no piece, an orange piece, or a green piece. I attempted to solve this problem using both machine learning and conventional computer vision. In the end I found using support vector machines to be an adequate way of solving the problem. The detailed notebook, with code, used to create this model is [here](/notebooks/chess_piece_presence.html).

There is a problem with this approach though. What happens in this case?

<center>
{% include image name="chessboard3.svg" alt="Chessboard diagram of the Saavedra position." width="35%" %}
{% include arrow %}
{% include image name="whichpromotion.svg" alt="Chessboard diagram with colored circles asking, 'should the pawn be promoted to a rook or a queen?'" width="35%" %}
</center>
{% include caption txt="A famous endgame situation: <a href=\"https://en.wikipedia.org/wiki/Saavedra_position\">The Saavedra position</a>." %}

We could assume that the player will always promote their pawn to a queen, but this is [not always what is best](http://www.chessgames.com/perl/chesscollection?cid=1000028). Instead we can attempt to determine the type of piece using a more complex model. In [this notebook](/notebooks/chess_piece_classification.html) I attempt to use convolutional neural networks and TensorFlow to detemine what type of piece the player promoted their pawn to.
