---
layout: post
title: "John Carmack and magic numbers"
description: "John Carmack did Joe Rogan's podcast this week. To accompany the link to the video, I take the opportunity to highlight the most famous magic number ever created."
date: 2019-08-31
feature_image: images/post_images/carmack.jpg
tags: [programming]
---

John Carmack did Joe Rogan's podcast this week. To accompany the link to the interview, I take the opportunity to highlight the most famous magic number ever created.

<!--more-->

John Carmack is the quintessential engineer and a personal hero of mine. His interview with Joe Rogan is a fascinating narrative through his life, one that I highly recommend you listen to.

<iframe src="https://www.youtube.com/embed/udlMSe5-zP8" width="500" height="281" frameborder="0" webkitallowfullscreen mozallowfullscreen allowfullscreen></iframe>

Since I'm sending you to the interview anyway, there is not much sense in repeating the points here, so let's jump to another interesting topic instead.

One of the many contributions of Carmack to the industry was his insistence on open sourcing all of id Software's games. That's the reason that you can run *Doom* on anything from toasters to oscillators today. When *Quake III* was open sourced, the community was treated to this gem:  

```javascript
float Q_rsqrt( float number )
{
    long i;
    float x2, y;
    const float threehalfs = 1.5F;

    x2 = number * 0.5F;
    y  = number;
    i  = * ( long * ) &y;                       // evil floating point bit level hacking
    i  = 0x5f3759df - ( i >> 1 );               // what the...? 
    y  = * ( float * ) &i;
    y  = y * ( threehalfs - ( x2 * y * y ) );   // 1st iteration
//  y  = y * ( threehalfs - ( x2 * y * y ) );   // 2nd iteration, this can be removed

    return y;
}
```  

_   

This function produces a (very good) approximation for the inverse of the square root of a float32 number. At the time, it managed to be way faster than the standard library equivalent, as it avoided expensive CPU operations like division and multiplication. How does it work though? And what's with that 0x5f3759df number? 

That is the question isn't it? The [fast inverse square root](https://en.wikipedia.org/wiki/Fast_inverse_square_root) function has fascinated two generations of programmers by this point. Understanding how it works has proven a very memorable right of passage for a lot of people. 

{% include image_caption.html imageurl="/images/post_images/inverse.jpg" title="0x5f3759df" caption="There are T-Shirts even!" %}

So what do you think? Do you want to give it a shot? 

This [paper](http://www.lomont.org/papers/2003/InvSqrt.pdf) by Chris Lomont goes through the math behind this function in great detail if you need some hints.
