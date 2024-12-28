---
title: 'Batch-Converting Video Files with Handbrake and Python'
date: Sun, 07 Aug 2016 19:35:17 +0000
draft: false
tags: ['Programming']
thumbnail: 
    src: "/blog/batch-converting-video-files-with-handbrake-and-python/media/headline.png"
    visibility:
        - list
categories:
    - "Blog"
---

Over the past few years, I have acquired quite a video collection. Hundreds of files have been downloaded or DVDs ripped, resulting in nearly a terabyte of television and movies. I usually use Handbrake to rip files off a DVD, since it has several parameters that can be tweaked to give the ideal video output. I can easily name the file, specify an output format, and even create a queue for getting multiple videos from a disk. Handbrake does a great job with movies, since often it is a single file that needs extracting. TV shows are a bit more cumbersome, due to multiple disks being involved. For this reason, I have developed the following workflow to easily rip TV shows from a DVD set.

1.  I start by using MakeMKV to rip individual episodes from a disk. Its simple interface allows for quick renaming of files, and the extraction process takes a matter of minutes for each disk. I like to use a short, standard naming scheme for the episodes, such as "0103" for "Season 1, Episode 3"

    ![MakeMKVrename1](media/makemkvrename1.png?w=193)

2.  After extracting, I quickly skim the videos, making sure I have labeled everything correctly. I haven't encountered any disks that kept the episodes out of order, but sometimes there are special features I want to keep. In this particular case, some of the episodes of Daria had introductions by the characters at the beginning. I've found the easiest way to skim is with VLC. It has a small footprint when used, and can read just about any file format.
3.  Unfortunately, the output files from MakeMKV are huge, usually  around 800 MB for a short, 20 minute video. Using the Handbrake command line interface (CLI) and Python, I wrote a small script that could rename the files and convert them to a smaller file. The comments of the script are pretty clear, but the main gist of the program is to:
    1.  Scan a given directory for files
    2.  Make a list of found files
    3.  Go through the list, converting videos and renaming to fit the naming scheme.

{{< highlight python >}}
# This script reads the *.mkv files in a given directory, and uses Handbrake
# to convert them to a smaller filesize and specific naming scheme.
# This script written by Geoffrey Tuttle
 
# Import necessary modules
from pathlib import Path  # manipulates computer directories
from subprocess import call  # call(command, shell=True) runs command from cmd
 
# Define locations and show name
input_path = Path('C:/Users/geoff_000/Videos/MakeMKV')  # Input directory
output_path = Path('E:/Videos/TV/Daria/')  # Output directory
show_name = 'Daria'
 
# Create list of files to be converted
handles = [x.stem for x in input_path.iterdir()]
input_path = 'C:\\Users\\geoff_000\\Videos\\MakeMKV\\'
output_path = 'E:\\Videos\\TV\\Daria\\'
 
# Iterate through list of video files, converting them using Handbrake
for old_filename in handles:
    # Create new filename in a readable format for Plex
    new_filename = show_name + ' S{}E{}'.format(old_filename[:2],
                                                old_filename[2:])
    # Create the command for running Handbrake for each path and filename
    command = ('"C:\Program Files\HandbrakeCLI\HandBrakeCLI.exe" -v0 -i "' +
               input_path + '{}.mkv" -o "'.format(old_filename) + output_path +
               '{}.mp4" --preset="Normal"'.format(new_filename))
 
    print(old_filename + ' conversion started')
    call(command, shell=True)
    print(new_filename + ' converted')
{{< / highlight >}}

I had just finished up the script and ran a short test when Windows gave me an “Imminent Hard Drive Failure” warning for my Media drive. Instead of taking a chance to going ahead with running this script, I powered down the computer, hopped on my laptop, and ordered a replacement drive so I can back up everything.

Once the drive arrives and I have copied everything. I will continue with running the script and post a short update to this post.