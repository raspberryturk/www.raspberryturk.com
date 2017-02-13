---
title: Daemon
layout: page
---

# Introduction

The Raspberry Turk runs on the Raspberry Pi in a daemon process called [`raspberryturkd`](https://bitbucket.org/joeymeyer/raspberryturk/src/719a3178aa94490fd08c851b1373a6674c14db82/raspberryturk/embedded/raspberryturkd.py?at=master&fileviewer=file-view-default). It is based on [`python-daemon`](https://pypi.python.org/pypi/python-daemon), which handles setting up stdin/stdout/stderr, easy signal handling, running a single process using a PID lock file, detaching the process to run in the background, and various other helpful features. The deamon process is controlled through a helpful script located in the [`__main__.py`](https://bitbucket.org/joeymeyer/raspberryturk/src/719a3178aa94490fd08c851b1373a6674c14db82/raspberryturk/__main__.py?at=master&fileviewer=file-view-default) file of the project. This script is installed via [`setup.py`](https://bitbucket.org/joeymeyer/raspberryturk/src/719a3178aa94490fd08c851b1373a6674c14db82/setup.py?at=master&fileviewer=file-view-default) as a console script entry point.

```
$ raspberryturk --help
usage: raspberryturk [-h] [-v] {start,stop,restart,status} ...

Utility for starting and stopping the raspberryturk daemon (raspberryturkd).

positional arguments:
  {start,stop,restart,status}

optional arguments:
  -h, --help            show this help message and exit
  -v, --version         show program's version number and exit
```

# Agent

The [`raspberryturkd`](https://bitbucket.org/joeymeyer/raspberryturk/src/719a3178aa94490fd08c851b1373a6674c14db82/raspberryturk/embedded/raspberryturkd.py?at=master&fileviewer=file-view-default) is responsible for running a perception/action sequence in a loop until it encounters an interrupt signal. The perception/action sequence exists in [`agent.py`](https://bitbucket.org/joeymeyer/raspberryturk/src/719a3178aa94490fd08c851b1373a6674c14db82/raspberryturk/embedded/agent.py?at=master&fileviewer=file-view-default) and takes the following form:

1. Get the last know game state from [PGN](https://en.wikipedia.org/wiki/Portable_Game_Notation) saved on disk 
2. If it's my turn
	1. Decide where to move
	2. Move piece using robotic arm
	3. Update game state in the PGN
3. If it's not my turn
	1. Use camera to see if a move has been made
	2. If it has, wait several seconds and double check
	3. If it is confirmed, update the game state in the PGN
4. Write status to disk
5. Repeat from step 1

_Image of perception/action sequence_

# Status

Every loop of the sequence a status of the current game is written to `/var/lib/raspberryturk/status.txt`. This includes information like the which color pieces appear to be on which squares (part of the [vision](/details/vision.html) algorithm), and the current state of the game saved to disk. There is a useful command to watch the file for changes, `$ raspberryturk status --watch`.

_Animation of status updating when the board changes_

# Logging

The project uses logging for various tasks and has come in useful when debugging. In scripts, logging is printed to the console. In the daemon, it is handled by [`RotatingFileHandler`](https://docs.python.org/2.7/library/logging.handlers.html#logging.handlers.RotatingFileHandler).

Excerpt from logs:
```
2016-12-14 20:56:39,194 - raspberryturk.embedded.motion.coordinator - INFO - Done moving piece (elapsed time: 36.1295258999s).
2016-12-14 20:56:39,195 - raspberryturk.embedded.game - INFO - Applying move g7g5...
2016-12-14 20:56:39,320 - raspberryturk.embedded.game - INFO - Saving game '/var/games/raspberryturk/game.pgn'...
2016-12-14 20:56:39,482 - raspberryturk.embedded.game - INFO - Done saving game '/var/games/raspberryturk/game.pgn'.
2016-12-14 20:56:39,483 - raspberryturk.embedded.game - INFO - Syncing games...
2016-12-14 20:56:39,816 - raspberryturk.embedded.game - INFO - Sync games successful.
2016-12-14 20:56:39,818 - raspberryturk.embedded.game - INFO - Applied move g7g5.
2016-12-14 20:56:41,018 - raspberryturk.embedded.agent - INFO - Game has ended, result: 0-1
2016-12-14 20:56:41,020 - raspberryturk.embedded.game - INFO - Starting new game '/var/games/raspberryturk/1481767001.pgn'...
2016-12-14 20:56:41,021 - raspberryturk.embedded.game - INFO - Saving game '/var/games/raspberryturk/1481767001.pgn'...
2016-12-14 20:56:41,025 - raspberryturk.embedded.game - INFO - Done saving game '/var/games/raspberryturk/1481767001.pgn'.
2016-12-14 20:56:41,026 - raspberryturk.embedded.game - INFO - Syncing games...
2016-12-14 20:56:41,263 - raspberryturk.embedded.game - INFO - Sync games successful.
2016-12-14 20:56:41,265 - raspberryturk.embedded.game - INFO - Entered game /var/games/raspberryturk/game.pgn -> 1481767001.pgn
2016-12-14 20:56:41,266 - raspberryturk.embedded.game - INFO - Syncing games...
2016-12-14 20:56:41,387 - raspberryturk.embedded.game - INFO - Sync games successful.
2016-12-14 21:02:47,000 - __main__ - WARNING - Received signal 15.
2016-12-14 21:02:47,001 - __main__ - INFO - Stopping RaspberryTurkDaemon.
```
