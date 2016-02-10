---
layout: post
title: "Moving in circles"
date: "2016-02-11 0:00:00 +0100"
comments: true
category: "GAME"
tags:
 - 5SD033
---

If you want to move around a 2D planet while still being able to loop around, you have a few options. You could have the ground be flat like any other 2D game and loop one edge to the other, having your characters move just like any other 2D game. Or you could make the ground an actual circle and make your characters orbit around it using basic trigonometrics. We (or, well, I) decided to do the latter.

<video autoplay loop>
  <source src="https://λ.pw/E7w.mp4" type="video/mp4">
</video>

The reasoning behind that was because we wanted a nice curvature on the surface on the planet so that the player quickly understands that the level is in fact a circular planet and that you will loop around if you move far enough.

In order to achieve that, we had to either rely on some good old math, use some fancy shaders (which is an uncharted, scary territory for me) or use some clever method that we didn't think of. So the obvious choice was math, especially so since I remembered the math behind it. While the it isn't very advanced, the math can be hard to wrap your head around if you aren't familiar with trigonometry.

Here's literally all the code that takes care of placing the player on the surface on the planet:

```cpp
float angleRadians = angle * M_PI / 180;
setPosition(cosf(angleRadians) * radius, sinf(angleRadians) * radius);
```

Here's an image so you maybe can visualize it a bit easier:

![you got to admit, it is a pretty nice triangle](http://i.imgur.com/I5zfue8.png)

All we need to use for positioning are the angle and radius, since we can calculate `x` and `y` from those using these mathemagical functions called `sin()` and `cos()`. If you do `cos(angle) * radius`, you get `x`. If you do `sin(angle) * radius`, you get `y`. There's quite a lot more to this, but this is all we need in order to calculate the position from an angle and radius.

```cpp
float angleRadians = angle * M_PI / 180;
```

This line is there in order to convert degrees into radians. That's because the math library functions uses radians, but I stored the angle in degrees since that's what all rotation functions in SFML uses.

All that's left is to move the actual player, which is simply done by increasing/decreasing the angle in order to move it around the circle and increase/decrease the radius in order to move the player closer/further away to the circle.

The thing about using this method is that I don't really know how to do proper 2D platformer collisions using this, but that's irrevelant for our game so I don't have to care about that, for now.
