# Jamman looper SD card sync tools

I'm using this set of scripts to sync my Jamman Solo XT loops over SD card since
Jamman Solo XT is not supported in Linux i.e. it doesn't mount as a USB storage
device. Luckily it support SD card so loops can be easily transferred this way.

These scripts are simple wrappers around rsync and ffmpeg and can be easily
modified for other loopers which support SD cards.

## Preparation

### SC card setup

To prepare SD card for usage with Jamman looper you can see Users Manual. The
procedure boils down to:

1. Insert SD card into your Jamman looper and format it by long pressing `store`
   button until `For` displays on the screen. Confirm with `store` button.
2. Rewind loop number to 200 and press up again. You should see `1` but the
   `CARD MEM` LED will be lit.
3. Save a loop in order to see how files are organized on the card. Basically,
   files/folders are organized like this:
   
   ```
    ├── Patch01
    │   ├── patch.xml
    │   └── PhraseA
    │       ├── phrase.wav
    │       └── phrase.xml
    └── Patch02
        ├── patch.xml
        └── PhraseA
            ├── phrase.wav
            └── phrase.xml
   ```
   
Note: The script `jamman-newloop` expect that loop 01 exists so be sure to
record the loop 01 on your looper when starting to use this script.

### Installation

Clone this repo and make sure it is on your PATH. If you have a folder for
scripts in your PATH already you can just link scripts from this folder.

E.g. if `~/bin/` is on your PATH:

```sh
$ git clone git@github.com:igordejanovic/jamman-sdcard-tools.git
$ ln -s jamman-sdcard-tools/jamman-* ~/bin/
```

Scripts are using `rsync` and `ffmpeg` be sure to install these packages.

### Configuration

In `jamman-config` there are two variables that should be configured:

- `JAMMAN_LOCAL` is the path to the directory on your computer where loops will
  be stored. This directory is synced back to your SD card so it serves as a
  staging place for loops preparation. Absolute path is used and the path should
  not end with `/`. See the example.
- `JAMMAN_MOUNT` is the place where the SD card is mounted when inserted.

## Usage

First thing you will probably want to do when insert the CD card is to sync your
loops to your computer. Just run

```
jamman-sync-from
```

You will see that your `JAMMAN_LOCAL` folder is populated by loops from the SD
card.

Now, you can create as many new loops as you want by `jammman-newloop`. This
script takes any audio or video file, extracts the audio in a proper format and
store it at the right place with required meta-data. You can also extract just a
part of the input file.

For example:

``` sh
# Extract audio from the given video and store as loop number 02
jamman-newloop SomeCoolVideo.avi 02
# Extract a part of some mp3 song and store it as loop 05
jamman-newloop SomeCoolSong.mp3 05 1:23 2:05
```

You can of course directly edit files in `JAMMAN_LOCAL` to your liking but keep
in mind that these scripts make sure you don't accidentally overwrite your SD
card loops by running `jamman-sync-to` before you have synced from the card. It
is done by creating `.new` files for each new loop and syncing to card only
those loops that has this file in their corresponding folder. Once the loops is
synced to the card the `.new` is deleted. So if you want to force syncing loop
to the card after e.g. manual editing you have to create `.new` file. To make
this easier there is a script `jamman-force` which will make `.new` file for
you. Just give it a loop number, e.g. `jamman-force 05`.


After finishing preparation of your loop just sync back to the card:

``` sh
jamman-sync-to
```

Unmount SD card, put it in your looper and you are ready to jam! :)


### Bonus

When a new loop is created (or loop is updated) by `jamman-newloop` a name of
the input file as well as from-time/to-time is keept in a file `.fromfile` (this
file is not synced to the SD card, it exists only in `JAMMAN_LOCAL`). This is
used to print nice messages during syncing but you can use this file as a
reminder what a particular loop is or to list all loops with this info using
the `jamman-list` script.

For example:

``` sh
# To get the full list
$ jamman-list > loops-list.txt

# Or to quickly find a loop
$ jamman-list | grep -i suzie
11 -- Creedence Clearwater Revival - Suzie Q-1mxaA-bJ35s.mp3

# Or to see what is in loop 12
$ jamman-list | grep ^12
12 -- Sweet Home Chicago style  E Blues Backing Track-J106bXVtKoQ.mp3
```


### Creating loops from YouTube videos

You can create loops directly from YouTube videos by `jamman-yt` script. It
works the same as `jamman-newloop` but the first param is not local file but a
URL to a YT video.

For example:

``` sh
# Download audio from the given link and store it as loop number 07
jamman-yt 'https://www.youtube.com/watch?v=8Pa9x9fZBtY' 07
```

You can also just take the part of the video:


``` sh
# Download audio, extract just the given time span and store it as loop 07
jamman-yt 'https://www.youtube.com/watch?v=8Pa9x9fZBtY' 07 9:29 9:47
```

This script calls [yt-dlp](https://github.com/yt-dlp/yt-dlp) so be sure to install it
before using this script.
