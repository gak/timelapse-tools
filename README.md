Time-lapse Tools
================

A command line tool to create time-lapse videos from individual images or other videos.

**NOTE**: This tool was released without much testing done on it. It can potentially be a dangerous tool because of this. Make sure you have backups of your files!

Requirements
------------
 * Uses ffmpeg to split and join videos, so it needs to be installed and in your path.
 * Runs with Python 3, so that needs to be installed too.

Features
--------
 * Split videos into images by a specified frame rate
 * Join individual images into a video
 * Adjust the number of images in a directory to a specified amount by removing frames at equal intervals
 * "Squash" files in a directory by renaming them to be ordered correctly for joining into a video.
 * When splitting videos, numbers within filenames are appropriately ordered, e.g.:
   * Image9, Image10, Image11 (instead of Image10, Image11, Image9)

A regular process
-----------------

A directory of 1000 images, from DSC_0112.JPG to DSC_1111.JPG:

    # ls my_images
    DSC_0112.JPG
    DSC_0113.JPG
    DSC_0114.JPG
    ...
    DSC_1111.JPG

Run squash to prepare them for ffmpeg:

**NOTE**: Did you back up your files?

    # ./tt squash --src=my_images
    myimages/DSC_0112.JPG -> myimages/00001.png
    myimages/DSC_0113.JPG -> myimages/00002.png
    myimages/DSC_0114.JPG -> myimages/00003.png
    ...
    myimages/DSC_1111.JPG -> myimages/01000.png

You have deicded that 1000 frames will make your video too long. You want to drop it down to 500 frames:

**NOTE**: Did I mention you should have a backup of your files?

    # ./tt remove_frames --src=my_images --target-frames=500
    current frames 1000
    target frames 500
    will remove 500 images
    will remove every 2 images
    Hit control-c to cancel, or enter to continue:

At this stage you can review how many files will be removed, and have a last chance to cancel. Hit enter and:

    Removing myimages/00001.png
    Removing myimages/00003.png
    Removing myimages/00005.png
    Removing myimages/00007.png 
    ...
    Removing myimages/00999.png

You will need to squash again:

    # ./tt squash --src=my_images
    myimages/00002.png -> myimages/00001.png 
    myimages/00004.png -> myimages/00002.png 
    myimages/00006.png -> myimages/00003.png 
    ...
    myimages/01000.png -> myimages/00500.png 

Now we can encode them:

    # ./tt join --src myimages --dst myvideo.mp4
    ffmpeg -i "myimages/%05d.png" -vcodec libx264 -vpre slow -crf 20 -threads 0 "myvideo.mp4"
    FFmpeg version 0.6.1, Copyright (c) 2000-2010 the FFmpeg developers
    Input #0, image2, from 'small/%05d.png':
      Duration: 00:00:02.60, start: 0.000000, bitrate: N/A
        Stream #0.0: Video: png, rgb24, 1920x1080, 25 fps, 25 tbr, 25 tbn, 25 tbc
    Output #0, mp4, to 'myvideo.mp4':
        Stream #0.0: Video: libx264, yuv420p, 1920x1080, q=10-51, 200 kb/s, 25 tbn, 25 tbc
    Press [q] to stop encoding
    frame=   60 fps= 14 q=25.0 size=     299kB time=10000000000.00 bitrate=   0.0kbits/s    

The default ffmpeg settings were appropriate for me, but you can change them by modifying tt itself. It basically is a high quality H.264 video with no audio.

Contact
-------

Im on Twitter as [@gakman](http://twitter.com/gakman/) or on [email](mailto:tt@gakman.com) if you wish to contact me.

