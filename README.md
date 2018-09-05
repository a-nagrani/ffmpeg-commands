# ffmpeg-commands
Collection of useful ffmpeg commands for processing audio and video files.  

## FFMPEG
[FFMPEG](https://www.ffmpeg.org/) is an extremely useful library for manipulating audio and video files. It is free and available for Windows, Mac and Linux. 

Here are some ffmpeg commands that I use frequently in my work (stored here so that I do not lose them). Some of the commands have been lifted directly from other sources (mostly from stack overflow), others have been modified. Some include simple bash programming so that they can be applied to an entire folder at a time. 

Disclaimer: The examples here often do not explain all the different option flags available. If you require a full thorough list, please read the [documentation here](https://www.ffmpeg.org/ffmpeg-all.html), or use the terminal command `man ffmpeg-all`.

This list will be updated with more commands as and when I use them. Contributions are welcome. 

##  Basics 

To display media file details (audio/video file information), run

``` 
ffmpeg -i video.mp4 
```

To check the list of supported formats by FFmpeg, run

``` 
ffmpeg -formats 
```

If you have ffplay installed, you can run 

```
ffplay video.mp4
```

to preview or test video or audio files in the terminal itself. On a mac, run ` brew install ffmpeg --with-sdl2` to install ffmpeg with ffplay. 

## (1) Extract a single image frame from a video
This command will extract the video frame at the 15s mark and saves it as a 800px wide JPEG image. You can also use the -s switch (like -s 400×300) to specify the exact dimensions of the image file though it will probably create a stretched image if the image size doesn’t follow the aspect ratio of the original video file.

```
ffmpeg -ss 00:00:15 -i video.mp4 -vf scale=800:-1 -vframes 1 image.jpg 
```

## (2) Extract all frames from a video
Extracts frames at a frame rate of 1 frame per minute. Most TV shows and other videos are [interlaced](https://en.wikipedia.org/wiki/Interlaced_video), this command includes the deinterlacing flag. By default ffmpeg tries to use as many threads as it can, the -threads flag can be set to 1 to force it to only use a single thread. 

```
ffmpeg -i video.mp4 -threads 1 -deinterlace -q:v 1 -vf fps=1/60 frame%03d.jpg 
```

## (3) Convert video from one format to another
You can use the -vcodec parameter to specify the encoding format to be used for the output video. 

```
ffmpeg -i youtube.flv -c:v libx264 filename.mp4 
```

If you want to speed up the process you can force a preset (however this will degrade the quality of the output video).

```
ffmpeg -i video.wmv -c:v libx264 -preset ultrafast video.mp
```

## (4) Convert all the videos in a folder to mp4 

```
for i in *.avi; 
do name=`echo $i | cut -d'.' -f1`; 
echo $name; 
ffmpeg -i "$i" -vf yadif "${name}.mp4"; 
done 
```

## (5) Extract a smaller clip from a video file 
You can use the time offset parameter (-ss) to specify the start time stamp in HH:MM:SS.ms format while the -t parameter is for specifying the actual duration of the clip in seconds.

``` 
ffmpeg -i input.mp4 -ss 00:00:50.0 -codec copy -t 20 output.mp4 
```

## (6) Concatenate video files (all files must have the same codec)

```
ffmpeg -f concat -i file-list.txt -c copy output.mp4 
```

## (7) Convert a video into animated GIF
This command converts videos into animated GIFs. Use the scale filter to specify the width of the GIF, the -t parameter to specify the duration and -r to specify the frame rate (fps).

```
ffmpeg -i video.mp4 -vf scale=500:-1 -t 10 -r 10 image.gif 
```

## (8) Create a video from frames
frame rate specified by the -r flag, -start_number to specify the fist frame of the desired output video. 

```
ffmpeg -r 1/25 -start_number 9999 -i 000%05d.jpg -c:v libx264 -vf fps=25 -pix_fmt yuv420p out2.mp4
```

## (9) Extract only the audio from a video
The -vn switch extracts the audio from a video, the -ab switch saves the audio as a 256kbps (bit rate) audio file.

```
ffmpeg -i video.mp4 -vn -ab 256 audio.mp3
```

Useful additional flags: 

-vn – Remove video from the output file.

-ar – Set the audio frequency of the output file. Commonly used values: 22050, 44100, 48000 Hz.

-ac – Set the number of audio channels.

-ab – Set the audio bitrate.

## (10) Batch convert audio to single channel with a sampling frequency of 16kHz 
Convert all wav files in a folder to single channel 16kHz. 

```
for i in *.wav;
  do name=`echo $i | cut -d'.' -f1`;
  echo $name;
  echo $i;
  ffmpeg -i  $i  -acodec pcm_s16le -ac 1 -ar 16000 “${name}.wav"; 
done
```









