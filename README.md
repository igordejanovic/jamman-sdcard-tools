# Jamman looper SD card sync tools

I'm using this set of scripts to sync my Jamman loops over SD card since Jamman
Solo XT is not supported in Linux i.e. it doesn't mount as a USB storage device.
Luckily it support SD card so loops can be easily transferred this way.

These scripts are simple wrappers around rsync and can be easily modified for
other sd card syncing purposes.

## Preparation

To prepare SD card for usage with Jamman looper and these scripts do the following:

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
   
## Scripts usage

Open `config` file and set `JAMMAN_LOCAL` variable to point to the directory on
your local disk where you want to keep your loops.

In order to prepare a loop on your computer:

1. run `jamman-sync-from` script to sync all loops to your local disk.
2. Copy `PatchXX` folder and rename it accordingly.
3. Replace `phrase.wav` with the new loop.
4. Repeat for each new loop. You can investigate `phrase.xml` for some option
    you can define.
5. To sync back all loops to your SD card run `jamman-sync-to`

**Warning:** Both scripts overwrite the target so be sure that you are running
the right script.

## Tips

To check audio file info and metadata your can use `mediainfo` tool.

```sh
$ ~/JammManLoops/Patch01/PhraseA » mediainfo phrase.wav
General
Complete name                            : phrase.wav
Format                                   : Wave
File size                                : 1.02 MiB
Duration                                 : 6 s 45 ms
Overall bit rate mode                    : Constant
Overall bit rate                         : 1 411 kb/s

Audio
Format                                   : PCM
Format settings                          : Little / Signed
Codec ID                                 : 1
Duration                                 : 6 s 45 ms
Bit rate mode                            : Constant
Bit rate                                 : 1 411.2 kb/s
Channel(s)                               : 2 channels
Sampling rate                            : 44.1 kHz
Bit depth                                : 16 bits
Stream size                              : 1.02 MiB (100%)

```

To convert audio file to the appropriate `phrase.wav` needed format:

```sh
ffmpeg -i mynewloop.mp3 -acodec pcm_s16le -ac 2 -ar 44100 -map_metadata -1 phrase.wav
```

