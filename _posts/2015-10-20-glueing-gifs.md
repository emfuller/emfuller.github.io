---
layout: post
date: 2015-10-20
title: Never forget: how to merge two gifs together
summary: example code for sewing together two gifs of animal movement into a single gif using imagemagick
categories: snippets
---

When I work with fine-scale movement data I inevitably want to make movies of them and share with collaborators. Many iterations later, I have long since lost my code (and the data) that I originally used to make these gifs but am too lazy to regenerate.

I need to sew these two gifs together, and below is how I  figured out how to do it. [This answer](http://unix.stackexchange.com/a/53443) from stack overflow got me going in the right direction, but I modified the script a bit.

Will need a total of three folders, one for each of the gifs plus one for the sewn version. Put the original gifs in the first and second folder.

```
mkdir first
mv moose_step.gif first/

mkdir second
mv moose_step-2.gif second/

mkdir third
cd third/
```

Unpack both gifs and rename based on image number

```
cd first/
convert moose_step_simple.gif x%04d.gif
cd ..

cd second/
convert moose_step-2.gif x%04d.gif
cd ..
```

Navigate to third folder, this is where the concatenated gif will end up. I'm using the `montage` function which puts two images together and looping across all files. The `-geometry` argument is important as it determines how the images fit together.

In this case `moose_step_simple.gif` is a 800x800 px image, and `moose_step-2.gif` is an 800x200 px image and I want stack `moose_step_simple.gif`  above the `moose_step-2.gif`.


```
cd third/
for filename in ../first/*
do
	  filename=`basename $filename`
	    montage -tile 1x2 -geometry 800x800 ../first/$filename ../second/$filename concat$filename
    done
```

My hack was to change the original code to `-tile 1x2` which I'm guessing means one row, two columns. I changed the geometry argument to the largest image (800x800) which means there's some awkward whitespace around the 800x200 image. But because the backgrounds both white it doesn't matter.

I should note: running this gives me the error
```
montage: unable to read font `(null)' @ error/annotate.c/RenderFreetype/1152.
```
 which I ignore. I think it has something to do with the correct fonts installed (as per [here](http://www.imagemagick.org/discourse-server/viewtopic.php?t=20529)). But since neither of these images have font, I can get away without having to worry about it.

 Finally to make a new output, put together the images, also optimize and remove background to make the final gif smaller (goes from 18 Mb to 1.2 Mb roughly). 

```
 convert -layers optimize -background None concatx* output.gif

  rm -rf ../first
  rm -rf ../second
  rm concat*
```
