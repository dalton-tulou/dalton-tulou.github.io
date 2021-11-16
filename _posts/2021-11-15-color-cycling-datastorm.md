---
layout: post
author: Dave
title:  "My color cycling efforts at Datastorm"
categories: [software]
tags: [amiga, deluxepaint, color cycling]
---

I just learned that I could use Pro Motion to convert my color cycling ILBM pictures to GIF for posting on the web. So naturally I went ahead and loaded the pictures I submitted to the color cycling competitions at Datastorm 2017, 2018 and 2019. I will elaborate a little bit on how I created them.

## 2017: "Bad Ring"

This picture uses the technique of assigning unique color indices to each frame of animation. This works as long as the frames have no overlap. If you look closely at the picture you'll see that there are some overlap in a few places and the produces artefacts. But in all, mainly becayse the dots are rather small and move rather fast, the picture mostly holds together.

![Bad ring](/assets/images/bad ring.gif)

An animated gif (which I can't find now) served as artistic model for this picture. I'm pretty sure the author was *dvdp* who in turn I believe in some of his work must have been inspired by color cycling animations of the past.

## 2018: "Drop on"

This picture uses the same technique as above. One "lit" color index travels through the palette and shows different frames of animation. The overlap in this picture is substantial, but because the dots are so small this mostly manifests as slight flickering. The viewers brain mostly compensates for this imperfection. My only gripe with this picture is that there is one point in the animation when there are almost no dots visible which gives sort of spoils the sense of continuous movement for me. I did not notice this until after I had submitted the picture for the competition.

![Drop on](/assets/images/drop on.gif)

I'm adding the image with pro motions default palette too, just so you can see how crammed with pixels it actually is.

![](/assets/images/drop on default palette.gif)

Again, this was modeled after a gif animation by *dvdp*.

## 2019: "Big wheel"

This picture was inspired by [Farfars 3D color cycling image of 2018](https://demozoo.org/productions/191203/). So I used Blender to model a very basic 3D scene and export UV-coordinates as well as a brightness map. I used dithering to convert the brightness to just a few levels, and then I use parallel cycle ranges for each shade of brightness. I was very happy with how this came out.

![Drop on](/assets/images/big wheel.gif)
