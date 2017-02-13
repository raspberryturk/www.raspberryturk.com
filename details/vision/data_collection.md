---
title: Data Collection
layout: page
---

_Same logo image that is on the kaggle dataset_

# Introduction

The [vision](/details/vision.html) aspect of the Raspberry Turk is based on a large dataset which consists of overhead images and the associated board state for each image. This dataset is publicly [available](https://www.kaggle.com/joeymeyer/datasets) on Kaggle, released under the [(CC BY-SA 4.0) license](https://creativecommons.org/licenses/by-sa/4.0/).

# Collection

Collection occured over several hours as I sat moving pieces very slightly. I took roughly 4 images of each piece in each square, in a different position/rotation in the square, and under different lighting conditions. Each frame captured includes:

| Type | Number per Frame |
|:----:|:----------------:|
| Empty | 30         			|
| White Pawn | 8     			|
| White Knight | 2   			|
| White Bishop | 2   			|
| White Rook | 2     			|
| White Queen | 2    			|
| White King | 1     			|
| Black Pawn | 8     			|
| Black Knight | 2   			|
| Black Bishop | 2   			|
| Black Rook | 2     			|
| Black Queen | 2    			|
| Black King | 1     			|

The [`collection.py`](https://bitbucket.org/joeymeyer/raspberryturk/src/719a3178aa94490fd08c851b1373a6674c14db82/raspberryturk/embedded/data/collection.py?at=master&fileviewer=file-view-default) script provides a simple GUI for capturing chessboard frames and associated [board FENs](https://en.wikipedia.org/wiki/Forsyth%E2%80%93Edwards_Notation).
```bash
$ python -m raspberryturk.embedded.data.collection --help
usage: raspberryturk/embedded/data/collection.py [-h] [-s SEED] base_path

Utility used to capture chessboard images and associated FENs. While the
script is running use 'r' to rotate the pieces on the board, 'c' to capture
the current frame, and 'q' to quit.

positional arguments:
  base_path             Base path for data collection.

optional arguments:
  -h, --help            show this help message and exit
  -s SEED, --seed SEED  Random seed to initialize board.
```

_Animation of moving pieces and rotating pieces_

# Raw Data

The [`collection.py`](https://bitbucket.org/joeymeyer/raspberryturk/src/719a3178aa94490fd08c851b1373a6674c14db82/raspberryturk/embedded/data/collection.py?at=master&fileviewer=file-view-default) script produces a series of folders which each contain 480x480px images and a `board.fen` file.

_Image of folder structure containing multiple images and board.fen with arrows to the actual image and of the board FEN_

# Interim

The [`process_raw.py`](https://bitbucket.org/joeymeyer/raspberryturk/src/719a3178aa94490fd08c851b1373a6674c14db82/raspberryturk/core/data/process_raw.py?at=master&fileviewer=file-view-default) script will take the full set of images and board FENs collected with collection.py and slice them up into labeled folders of individual 60x60px images. The script keeps track of which board images have been processed, so the next time the script is run it will only process newly captured images.

```
$ python -m raspberryturk.core.data.process_raw --help
usage: raspberryturk/core/data/process_raw.py [-h] [-x]
                                              source_path target_path

Utility used to process raw chessboard image data collected by collection.py.
It goes through the contents of SOURCE_PATH, processes the collected data, and
stores the interim format in TARGET_PATH. By default it will keep track of
what raw data has been processed so it can be run multiple times without
reprocessing the same data.

positional arguments:
  source_path         Source path for data processing.
  target_path         Target path for data processing.

optional arguments:
  -h, --help          show this help message and exit
  -x, --ignore_cache  If True, this will delete all contents in the
                      target_path and reprocess everything.
```

_Image of full board pointing to 64 slices images with an arrow to folder structure_

# Processed

The final step is to produce a complete encapsulated dataset that can be imported using [`np.load`](https://docs.scipy.org/doc/numpy-1.12.0/reference/generated/numpy.load.html). Each dataset contains `X_train`, `X_val`, `y_train`, `y_val`, and `zca` (if the dataset is [ZCA whitened](http://ufldl.stanford.edu/wiki/index.php/Whitening#ZCA_Whitening)) and can be customized in a number of ways as described below.

```
$ python -m raspberryturk.core.data.create_dataset --help
usage: raspberryturk/core/data/create_dataset.py [-h] [-g] [-r] [-s SAMPLE]
                                                 [-o] [-t TEST_SIZE] [-e] [-z]
                                                 base_path
                                                 {empty_or_not,white_or_black,color_piece,color_piece_noempty,piece,piece_noempty}
                                                 filename

Utility used to create a dataset from processed images.

positional arguments:
  base_path             Base path for data processing.
  {empty_or_not,white_or_black,color_piece,color_piece_noempty,piece,piece_noempty}
                        Encoding function to use for piece classification. See
                        class_encoding.py for possible values.
  filename              Output filename for dataset. Should be .npz

optional arguments:
  -h, --help            show this help message and exit
  -g, --grayscale       Dataset should use grayscale images.
  -r, --rotation        Dataset should use rotated images.
  -s SAMPLE, --sample SAMPLE
                        Dataset should be created by only a sample of images.
                        Must be value between 0 and 1.
  -o, --one_hot         Dataset should use one hot encoding for labels.
  -t TEST_SIZE, --test_size TEST_SIZE
                        Test set partition size. Must be value between 0 and
                        1.
  -e, --equalize_classes
                        Equalize class distributions.
  -z, --zca             ZCA whiten dataset.
```

Datasets can be created for different purposes. For example, in the [`chess_piece_presence.ipynb`](/notebooks/chess_piece_presence.html) notebook, the dataset included color images with white/black/empty as the label. In the [`chess_piece_classification.ipynb`](/notebooks/chess_piece_classification.html) notebook, the images are grayscale, the features are preprocessed using ZCA whitening, and the labels are king/queen/rook/bishop/knight/pawn.

_Table/image showing the contents of a dataset_
