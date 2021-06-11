# Genesis/Mega Drive VGM Logging and Trimming Guide

This guide should break down the necessary steps to get yourself logging vgm from Sega Genesis games, and then trimming them to be suitable for the Project2612 website. Currently it may appear that there are already a lot of games on the website, however many of these vgm's need to be checked and possibly re-logged so you can help us out a great deal!

The following guide will be for Windows users until anyone comes up with guides of Linux/Mac users and wants to contribute.

**Note:** I am looking for help completing this guide. I am open to suggestions submitted via pull request. Thanks for any help you can provide.

## Sega Genesis VGM Logging

The first thing you will need to log is the current nightly build of BlastEm! that has been adjusted for the VGM logging behavior. No other emulator is acceptable any longer for future Sega Genesis logging. Below are the links:

Windows 64-bit: [Windows 64-bit March 10th, 2021](https://www.retrodev.com/blastem/nightlies/blastem-win64-0.6.3-pre-a61b47d5489e.zip)  
Windows 32-bit: [Windows 32-bit March 10th, 2021](https://www.retrodev.com/blastem/nightlies/blastem-win32-0.6.3-pre-a61b47d5489e.zip)

We suggest you use the Windows 64-bit version as that is the one that was used for this guide. The source is included in case anyone more technical wants to compile it themselves or inspect the code. The default.cfg may or may not need to be configured in your case. Default behavior is documented in the readme contained within the Blastem! archive.

To begin logging sound to a VGM file all you have to do is press the `m` key on your keyboard. To stop logging sound to a VGM file just press the `m` key again, and it will finish. Depending on your default.cfg settings for BlastEm!, that file will be placed somewhere and will be named something like `blastem_yyyymmdd_144300` with yyyymmddd being year, month, and date respectively. We suggest you change the name of this file to have the track number a hyphen and then the track name, such as `01 - Opening Theme.vgm` as you log each track, to make things easier.

Continue logging all of the VGMs you can from the game and organizing them this way, we will learn tagging next.

## Tagging

Next we need to tag the VGM we created. Tags are similar to MP3 tags which have info about the VGM file, title, author, game, creator of the VGM (you), etc... 

First download VGMToolbox:

https://sourceforge.net/projects/vgmtoolbox/

This is one of the easier tools to manipulate VGM files. We are just going to be using it for tagging. Open it up and go to Misc Tools\VGM Tools\VGM Tag Editor in the sidebar menu. Now select all of the VGM you logged and click drag them into the bigger of the two white rectangles under "Source Files" in the Tag Editor window. Let's set up the stuff that is generic to this entire log. Select all of the VGM's in the files list in the tag editor using shift+click. Then put a checkmark next to both of the Game fields, System fields, release date, and Ripper. Enter the relevant info in there and click "Update Tags" at the bottom.

Next individually update the Titles for each track, and add any comments as you think is relevant for each track. Once all tracks are sufficiently tagged, it's time to go to the next step.

## Trimming

Trimming is the process of finding the beginning and end of a track, and cutting the VGM down to size. It also includes "looping" which is to find the "loop point" in a track which normally loops indefinitely in-game. We will get you through how to do this. Before you begin trimming my advice is to make a copy of your tagged VGM above and store them in a different folder. I usually name this folder `raws` within the game folder where I am working. Do what works for you. I also make a `trimmed` folder which is where I dump all of my completed trimmed vgm.

Begin by downloading ValleyBell's latest set of tools and unzipping them to their own folder:

http://vgmrips.net/programs/tools/VGMTools_2016-08-04.7z (originally from here --> https://vgmrips.net/forum/viewtopic.php?f=3&t=207)

Personally, I copy my tools folder over to my working VGM trimming folder at this point. It just makes things easier. Go with what works for you. I also use a VGM Player in my workflow, this is the one from ValleyBell that I use:

https://vgmrips.net/programs/players/VGMPlay_050-1.7z (originally from here --> https://vgmrips.net/forum/viewtopic.php?t=112)

Next we need to make a simple batch script that will make a copy of all of our VGM files into a txt format that details the commands sent to the sound chips. Make a new file called `all2txt.bat` and enter the following into it and save:

```bat
FOR %%A IN (*.vgm) DO vgm2txt "%%A" 0 0
```

Now run that script. It will take some time if you have a lot of VGM in the working folder. This is running `vgm2txt.exe` on each .vgm file in the current folder. When it's complete we need to prepare our batch script for trimming.

Create a new batch file called `mktrim.bat` in the folder with your VGM files you will be working on and your VGMTools. Enter the following into the batch file and save it:

```bat
echo. > trim.bat
FOR %%A IN (*.vgm) DO echo vgm_trim "%%A" >> trim.bat
```

This should make a new batch script called `trim.bat`. Edit this file and you will see it should say something like this in it:

```bat
vgm_trim "01 - Opening Theme.vgm" 
vgm_trim "02 - Introduction.vgm" 
vgm_trim "03 - Stage 1.vgm" 
vgm_trim "04 - Stage 2.vgm" 
```

This is the file we will run after we enter the values for the Starting Point, Looping Point, and Ending Point of our VGM files. Now for the fun part. I suggest you use [Notepad++](https://notepad-plus-plus.org/downloads/) for the next step because these text files are huge and many other text editors can lock up even trying to load them. Load up your first txt file (e.g. `01 - Opening Theme.txt`)

Find the starting point of the track by searching for `Wait:` commands with a high number of samples. One of the easiest ways is to play the VGM in a vgm player and find the time it begins roughly, and correlate that to the text file. You will see something like:

```
0x000002C6: 61 8D 19    Wait:	2832 samples (   148.32 ms)	(total	6541 (00:00.15))
```

This indicates that at the time 00:00.15 in the song there had been 6541 samples total since the time I started logging. At the time on this line there were 2832 samples of `Wait:` occuring. Basically, nothing new. This might be a good time to start the track. Since the total to the right is also 6541, this means that the game had no sound when I started logging for the first 6541 samples it logged. Great! So we'll enter that as our first value in the trim.bat we are also editing:

```bat
vgm_trim "01 - Opening Theme.vgm" 6541
vgm_trim "02 - Introduction.vgm" 
vgm_trim "03 - Stage 1.vgm" 
vgm_trim "04 - Stage 2.vgm" 
```

Now the opening theme isn't a looping song in my case, so I'll tell vgm_trim that the song doesn't loop:

```bat
vgm_trim "01 - Opening Theme.vgm" 6541 0 
vgm_trim "02 - Introduction.vgm" 
vgm_trim "03 - Stage 1.vgm" 
vgm_trim "04 - Stage 2.vgm" 
```

Then I need to find the point when the track ends. Once again, I check with a VGM Player to get a rough time when the song stops, then I check the txt file:

```
0x00380AC8: 52 28 02    YM2612:		Channel 2 Key On/Off: Slot1 Off, Slot2 Off, Slot3 Off, Slot4 Off
0x00380ACB: 73          Wait:	 4 sample(s) (   0.09 ms)	(total	4124542 (01:33.53))
0x00380ACC: 52 28 04    YM2612:		Channel 3 Key On/Off: Slot1 Off, Slot2 Off, Slot3 Off, Slot4 Off
0x00380ACF: 74          Wait:	 5 sample(s) (   0.11 ms)	(total	4124547 (01:33.53))
0x00380AD0: 52 28 05    YM2612:		Channel 4 Key On/Off: Slot1 Off, Slot2 Off, Slot3 Off, Slot4 Off
0x00380AD3: 73          Wait:	 4 sample(s) (   0.09 ms)	(total	4124551 (01:33.53))
0x00380AD4: 52 28 06    YM2612:		Channel 5 Key On/Off: Slot1 Off, Slot2 Off, Slot3 Off, Slot4 Off
0x00380AD7: 61 FF FF    Wait:	65535 samples (   1486.05 ms)	(total	4190086 (01:35.01))
0x00380ADA: 61 FF FF    Wait:	65535 samples (   1486.05 ms)	(total	4255621 (01:36.50))
0x00380ADD: 61 FF FF    Wait:	65535 samples (   1486.05 ms)	(total	4321156 (01:37.99))
0x00380AE0: 61 FF FF    Wait:	65535 samples (   1486.05 ms)	(total	4386691 (01:39.47))
0x00380AE3: 61 FF FF    Wait:	65535 samples (   1486.05 ms)	(total	4452226 (01:40.96))
0x00380AE6: 61 FF FF    Wait:	65535 samples (   1486.05 ms)	(total	4517761 (01:42.44))
0x00380AE9: 61 FF FF    Wait:	65535 samples (   1486.05 ms)	(total	4583296 (01:43.93))
0x00380AEC: 61 FF FF    Wait:	65535 samples (   1486.05 ms)	(total	4648831 (01:45.42))
0x00380AEF: 61 F3 13    Wait:	5107 samples (   115.80 ms)	(total	4653938 (01:45.53))
```

So I will use 4124551 from the 6th line because the song ended roughly around 1:33 and this is when the long waits begin again as the song goes silent at the end. So I add that value:

```bat
vgm_trim "01 - Opening Theme.vgm" 6541 0 4124551
vgm_trim "02 - Introduction.vgm" 
vgm_trim "03 - Stage 1.vgm" 
vgm_trim "04 - Stage 2.vgm" 
```

Now I test this to see if I trimmed it right by saving the `trim.bat` I'm editing and running it. Listen to it, make sure it sounds right. If it doesn't, try again. Compare with the raw vgm you logged to be sure. Now this is an easy one because it just has a clear start and end.

## Looping

For looping tracks that middle value that we set to `0` earlier will have to indicate to the trimmer when the loop starts, and the third value indicates when it goes back to that point in time. Some songs play for 10 seconds, then they start the indefinitely looping portion for another 50 seconds, so this means we have a 1:00 song that loops at 0:10. For a 44,100 hz sampling rate (like the Sega Genesis) 10 seconds is 441000 samples, and 60 seconds is 2,646,000 samples. So if we imagined the loop began exactly at 10 seconds:

```bat
vgm_trim "02 - Introduction.vgm" 0 441000 2646000
```

This is what your values would look like in a song that already had a perfect trim at the beginning. In this case, the trimmed and looped vgm will play for 441,000 values, then play up to 2,646,000 samples and then go back to the 441,000 sample mark, infinitely looping.

To find the loop point you can try `vgmlpfind.exe` in ValleyBell's VGMTools kit. From the command line run `vgmlpfind.exe "02 - Introduction.vgm"` and use the defaults by pressing enter to the next prompts. You will see some information that can give you clues as to where the loop begins, and you can even sometimes be told exactly where the perfect loop is. If the lines have a ! in them, then use the values from that line. For example, some output from a VGM where it did find the loop:

```
VGM Loop Finder
---------------

File Name:      10 - Sniper.vgm
Step Size (default: 1):
Minimum Number of matching Commands (default: 1024):
Start Pos (default: 0 - auto):

Counting Commands ...  130915
Reading Commands ...  Done.
  Source Block            Block Copy            Copy Information
Start     Time          Start     Time          Cmds    Time
137650  00:03.12  !     2690414 01:01.01        99029   03:07.89
137813  00:03.13        438328  00:09.94        3783    00:06.80
137813  00:03.13        2990841 01:07.82        3783    00:06.80
137813  00:03.13        5543358 02:05.70        3783    00:06.80
137813  00:03.13        8095871 03:03.58        3783    00:06.80
```

In this instance, assuming I already had a first value of 10000, I would use the following for my values to get a perfect loop:

```
vgm_trim "10 - Sniper.vgm" 10000 137650 2690414
```

And that's easy! Now the hard part is if `vgmlpfind.exe` *doesn't* find the loop. If it doesn't then play the vgm and try to manually find it, and do a lot of trial and error adjusting the values based upon what you hear and what you see in the txt file until you get it just right. There is no simple way to walk you through that step, you have to develop the skills for it on your own.

## Compressing

Next we need to compress these VGM files for submission to the project2612 website and any other website hosting vgm files. Compressing can only be done after the file has been tagged and then trimmed. If you compress before trimming, you won't be able to trim those compressed files, even upon decompression.

Download optvgm here:

http://project2612.org/optvgm.zip

You may or may not need GZip for Windows installed as well. If so, go here:

http://gnuwin32.sourceforge.net/downlinks/gzip.php

Within the optvgm zip archive above, there is already a `optimise.bat` file. Edit that batch script by removing all of the text in it and replacing them with:

```bat
for %%f in (*_trimmed.vgm) do vgm_cmp -justtmr "%%f" "%%_cmp.vgm" 
for %%f in (*_cmp.vgm) do optvgm "%%f" "%%~nf.vgz" & ren "%%~nf_optimized.vgm.gz" "%%~nf_optimized.vgz"
```















## Tips and Tricks (ignore for wiki entry just personal stuff)

Modify your default.cfg file to place raw vgm into a folder depending on game name:

```cfg
	#path for storing VGM recordings, accepts the same variables as initial_path
	vgm_path $EXEDIR/vgm/$ROMNAME
```

This will make it easier to organize as you switch from game to game, and will store the vgm in the same directory as BlastEm!.

If (like me) you use an 8bitdo M30 controller with BlastEm! and are confused by the incorrect input binding (this is 8bitdo's fault), modify the following lines like so:

```cfg
pads {
		default {
			dpads {
				0 {
					up gamepads.n.up
					down gamepads.n.down
					left gamepads.n.left
					right gamepads.n.right
				}
			}
			buttons {
				a gamepads.n.a
				b gamepads.n.b
				rightshoulder gamepads.n.z
				x gamepads.n.x
				y gamepads.n.y
				leftshoulder gamepads.n.mode
				back gamepads.n.mode
				start gamepads.n.start
				guide ui.exit
				leftstick ui.save_state
			}
			axes {
				lefty.positive gamepads.n.down
				lefty.negative gamepads.n.up
				leftx.positive gamepads.n.right
				leftx.negative gamepads.n.left
				lefttrigger ui.save_state
				righttrigger gamepads.n.c
			}
		}
```
