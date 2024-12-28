---
title: "Image Scaling With Python"
date: 2020-09-29T00:55:33-04:00
thumbnail: 
    src: "/blog/hugo-image-scaling-with-python/media/Capture.PNG"
    visibility:
        - list
categories:
  - "Blog"
tags:
  - "Programming"
  - "Hugo"
  - "Portfolio"

draft: false
---

This blog is built using Hugo, a static website generator designed for quick build times and easy deployment. A static website requires no server backend, since everything is delivered in a *static* state to the reader (that's you!). All code is executed on the client machine. I love the lightweight simplicity of a static website, which brings up some nostalgia for the internet of the mid to late 90s, when these types of pages were the norm.

As I work on this blog, I find myself doing a lot of background tasks over and over again. The most time consuming is editing pictures. This page outlines the challenges and benefits of using low resolution images in this blog, along with a script I have written to streamline the editing process.

## Saving Space
![Sate](media/FullWithInset.jpg#center)
The image above might be the largest file on this site, a whopping 1.94 MB. Compared with most of the pictures I take with my camera or phone, this is one of the smaller ones. The average image size from my devices is about 4-5 MB. If I were to use these full resolution images, a single webpage could balloon up to nearly 50 MB. I pity the poor soul that tries to load up a 50 MB page on mobile data.

To save on space, each jpeg is scaled down so that the largest dimension is 1000px, and the quality is lowered from 95 to 75. This result is a huge reduction in file size. The scaled image is displayed below:
![Scaled](media/Scaled.jpg#center)
The scaled and reduced quality image takes up 46 KB. This is roughly 2.5% the original filesize, and much more reasonable to serve on a static page.

Here is a 400% zoom of the original image with an overlay of the edited version. Can you see where the image quality suffers?
![Inset](media/InsetZoom.jpg#center)
Looking closely, one might see a faint separation line running horizontal though the bottom of the diagonal strap buckle. Everything above is the original image, and everything below has been scaled down and compressed. There are a few artifacts in the empty spaces between the arms, but I cannot tell the difference when zoomed at 100%. Since the image still conveys the necessary information to support the page content, this is a great trade-off.

Scaling, adjusting quality, and renaming the files takes a good bit of time when I am putting together a page for the site. It is tedious, monotonous work that is ripe for automation. 

## Automate the Boring Stuff
Whenever I am faced with the challenge of writing some script to speed things up, I always consider this post from [xkcd](http://www.xkcd.com/):
![Automation](https://imgs.xkcd.com/comics/automation.png#center)
It is easy to fall into the efficiency trap and lose sight of the original task. Fortunately, I only got bogged down in debugging for a few hours over the weekend. Since this script will help me get more posts written and live, it was a worthy investment of time.

There is a lot of community support for python's Pillow image processing library, so I based my script around its functions.

### Get current folder name and generate file prefix
Before I start a new post, I collect relevant pictures into folders to help organize my thoughts. These folders are named with the month number, and a few words to describe the overall project or subject ("05 Generic Project Name"). The following snippet collects the all the files within a folder, and uses the folder name to generate a somewhat unique prefix for the images (05gpn). It also builds a list of suffixes so I can easily number the images.

{{< highlight python >}}
from PIL import Image
import os

cwd = os.getcwd()
folder = cwd.split("\\")[-1]
folder = folder.split(" ")
prefix = folder[0]
for each in folder:
    if each != prefix:
        prefix += each[0].lower()

suffix = ["01", "02", "03", "04", "05", "06", "07", "08", "09"]
for i in range(10, 100):
    suffix.append(str(i))
{{< / highlight >}}

For the last part, I could have written a clever bit of code to append "0" to digits 1..9, but this brings me back to the comic above. I did not want to fall into the "Rethinking" and "Ongoing Development" part of the graph...

### Parse file list, and apply scale and quality reduction
The next bit of code sifts through the filelist, removing anything with a "jpg" or "png" extension. I added the "item.lower()" to account for inconsistent case with extensions ("jpg" vs "JPG" are treated equally).

{{< highlight python >}}
filelist = os.listdir(cwd)
for item in filelist[:]:
    if not(item.lower().endswith(".png") or item.lower().endswith(".jpg")):
            filelist.remove(item)
{{< / highlight >}}

The script then iterates though the list of images, scaling them down. The if/else statement swaps the resize(x, y) arguments to account for portrait or landscape pictures. Lastly, tt will rename the files to a more manageable convention for hand typing, and saves them all to the specified quality.

{{< highlight python >}}
for item, name in zip(filelist, suffix):
    img = Image.open(cwd+"\\"+item)
    # Get image size to determine scaling
    size = img.size
    dim = (min(size) * 1000) / max(size)
    if max(size) > 1000:
        if size[0] == max(size):
            img = img.resize((1000,int(dim)), Image.ANTIALIAS)
        else:
            img = img.resize((int(dim), 1000), Image.ANTIALIAS)
    filetype = item.split('.')[1].lower()
    if filetype == 'png':
        img.save(cwd+"\\"+prefix+name+".png", 'png', quality=75)
    else:
        img.save(cwd+"\\"+prefix+name+".jpg", 'jpeg', quality=75)
{{< / highlight >}}

## End Result
Putting it all together, the complete script is below. It runs on every jpg and png in the working directory in which it is called.

{{< highlight python >}}
from PIL import Image
import os

# Get current folder name and generate file prefix
cwd = os.getcwd()
folder = cwd.split("\\")[-1]
folder = folder.split(" ")
prefix = folder[0]
for each in folder:
    if each != prefix:
        prefix += each[0].lower()

print("Image prefix is: " + prefix)

# Generate suffix 01, 02, 03...
suffix = ["01", "02", "03", "04", "05", "06", "07", "08", "09"]
for i in range(10, 100):
    suffix.append(str(i))

# List of included image files
filelist = os.listdir(cwd)
for item in filelist[:]:
    if not(item.lower().endswith(".png") or item.lower().endswith(".jpg")):
            filelist.remove(item)

for item, name in zip(filelist, suffix):
    img = Image.open(cwd+"\\"+item)
    # Get image size to determine scaling
    size = img.size
    dim = (min(size) * 1000) / max(size)
    if max(size) > 1000:
        if size[0] == max(size):
            img = img.resize((1000,int(dim)), Image.ANTIALIAS)
        else:
            img = img.resize((int(dim), 1000), Image.ANTIALIAS)
    filetype = item.split('.')[1].lower()
    if filetype == 'png':
        img.save(cwd+"\\"+prefix+name+".png", 'png', quality=75)
    else:
        img.save(cwd+"\\"+prefix+name+".jpg", 'jpeg', quality=75)
{{< / highlight >}}

It might not be that pretty, but it gets the job done and gets me back to making and writing.