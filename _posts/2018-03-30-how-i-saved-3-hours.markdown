---
title:  "How I saved more than 3 hours of editing with couple lines of code!"
date:   2018-03-30 14:00
categories: [python, pil, image editin]
tags: [python, pil, photography, image editing]
---


So... Imagine that you have this product photoshoot to do, and you have lots of products (let's say 35 materials with different patterns ) to shoot. You set up your cozy studio environment, lights tripods, choose the
perfect camera settings to bring the best out of each photo. You want to catch 3 angles of each product, so you get over 100 photos of products in the end. You clean up your studio
plug the camera into the computer and... ugh! so much to edit! But popular image editing software can come with a great help here! You quickly select photos that you
like the most, then edit it to look sweet and juicy, apply the same settings to all the other photos and bam! You just got yourself pretty nice product photoshoot!

You create new album on facebook, upload all of them, and then it hits you:

### How do the clients distinguish one from another? ###
{: style="color:gray; text-align: center; padding-bottom: 20px;"}

``` - "Oh, I want this yellow material with yellow stripes and blue dots!" ```

``` - "We have 3 of those patterns! which one do you want precisely?" ```

You already start to imagine a full day of answering this kind of questions, not counting the delivery mistakes that may happen on the way. You go back to the program and start to dig in the settings.
Uncle google starts to give you some hints on "How to watermark photos in [your-image-editing-software-name]". Another 30 minutes passed, but! There is a way! It can put the watermark sequence on your photo! Yay!

### Oh but wait a minute! ###
{: style="color:gray; text-align: center; padding-bottom: 20px"}

You can't just throw sequential numbers on each photo right? That would be confusing as some might have thought that they are separate products in some cases due to the pattern differences on each photo.

```Oh so I need to watermark them in groups somehow! So that each set of 3 photos of the same product has the same number on it!``` - you think.

So you renamed all the files so that they follow some logical naming pattern, and they are displayed in order:

<img src="{{ "images/materials_list.png" | prepend: site.baseurl }}" style="width:98%" alt="Materials List">

And then... Well, then comes the bad part.. you need to go through each set of photos, place watermark on them and export the... and do it again... and again... and AGAIN!

I lost my patience on the second set of photos... And started to think...

```Ok, and what if next time I will have 400 photos to edit and I will need to repeat this process not 35 but 140 times? There has to be an easier way to solve that problem... Can we make this process automatic?```

Luckily for me, `Python` programming language has a library called `PIL` (Python Imaging Library), that give you many possibilities of image transformations.

```Let's rock then! It cannot be that hard to make it work, and it can benefit in the future!``` - I thought.

After reading some lines of documentation I figured out that I can make some mask that behaves like a layer in popular image editing software, and then paste it into my photo. And even use my font for that! Yay!
Couple minutes later I came up with this simple function that watermarks photo with given number on the left upper corner:

```python
from PIL import Image, ImageDraw, ImageFont

def process_image(image_name, number):
    # watermark picture with number
    font = ImageFont.truetype("SignPainter.ttf", 85)
    main = Image.open(image_name)
    watermark = Image.new("RGBA", main.size)
    waterdraw = ImageDraw.ImageDraw(watermark, "RGBA")
    waterdraw.text((40, 40), str(number), font=font)
    main.paste(watermark, None, watermark)
    main.save("export/" + image_name, "JPEG")
```

Then because I already gave the photos proper names we just need to extract the proper photo number from the group:

```python
def watermark_number(file_name):
    # choose middle number from file name
    # example filenames: 'material-1-2.jpg', 'material-1.jpg'
    image_name = file_name.split('-')
    if len(image_name) == 3:
        return l[1]
    elif len(image_name) == 2:
        return l[1].split('.')[0]
```

And write some function to start the program in given directory for all .jpg files, and create the export folder for the new watermarked photos:

```python
import os

def main(directory='.'):
    all_files = os.listdir(directory)
    image_files = [i for i in all_files if i.endswith('.jpg')]
    if not os.path.exists('export'):
        os.makedirs('export')
    for image_name in image_files:
        print "processing image: " + image_name
        watermark = watermark_number(image_name)
        process_image(image_name, watermark)
    print 'processing finished.'
```

Peanuts!!! 20 minutes after research and some coding:

{% highlight shell %}
processing image: 96
processing image: 97
processing image: 98
processing image: 99
processing image: 100
processing finished.
{% endhighlight %}

And the job is done! A very small piece of software helped to save lots of time! And will help to save more in the future!

Final effect after adding watermark in footer as well:

<img src="{{ "images/material-1-2.jpg" | prepend: site.baseurl }}" style="width:98%" alt="Watermarked picture">

Interested in reading more? Since that time I extended this small project with some more cool features that simplify the process of grouping photos to watermark.
Make sure to check it out [here](https://github.com/pawel-krysiak/watermark-picture-python) !
