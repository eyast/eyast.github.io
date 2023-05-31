---
layout: page
title: At home 3D display
description: Transforming my Living Room TV into a Mind-blowing 3D
img: assets/img/puzzle_final/thumbnail.jpg
importance: 1
category: fun
---

<div class="row">
    <div class="col-sm mt-3 mt-md-0">
        {% include figure.html path="assets/img/puzzle_final/final.jpg" title="2000 pieces" class="img-fluid rounded z-depth-1" %}
    </div>
</div>
<div class="caption">
    A jigsaw puzzle composed of a single color, assembled with the help of Computer Vision to detect and describe edges, and find similarities amongst them. This was originally a 2000 pieces puzzle but I've lost a piece.
</div>

## Project Description

In the midst of the global pandemic, most of us found ourselves adjusting to a new normal – working from the comfort of our homes. As the days blurred together and time seemed to stretch endlessly, I, like many others, discovered that this newfound freedom came with ample opportunities for exploration and creativity. With extra time on my hands and a burning desire for a captivating home entertainment experience, I embarked on an exciting and ambitious project: transforming my living room TV into a mesmerizing 3D display with perspective-corrected visuals.

The idea struck me one evening as I was working from my living room, staring at the turned off TV. While my television offered hours of entertainment, I craved something more immersive – an experience that would transport me into the heart of the action, making me feel like a participant rather than a mere observer. With a passion for technology and a love for pushing boundaries, I set out to achieve the impossible within the confines of my own living room.

With a clear vision in mind, I dove headfirst into research, exploring technologies and existing DIY projects that hinted at the possibility of transforming a regular TV into a 3D display. I discovered that the key lied in leveraging the power of perspective correction – a technique that adjusts the projected image to match the viewer's position, creating a compelling illusion of depth and dimension. 

Armed with knowledge, determination, and an assortment of tools, I began the intricate process of modifying my television. It involved a combination of hardware modifications, software tweaks, and a fair share of trial and error. As I delved deeper into the project, I found myself facing technical challenges and embracing the learning curve that came with venturing into uncharted territory. It was an exhilarating journey that tested my problem-solving skills and ignited my passion for pushing the boundaries of what was possible.


## Solution Overview

The core of this innovative app revolves around harnessing the power of computer vision and utilizing a webcam to capture an image, subsequently analyzing it to determine the presence of a human. By leveraging PoseNet and a custom Neural Network built for purpose, the solution can swiftly identify and isolate the human figure within the image. Once the human presence is confirmed, the app goes a step further by extracting the body position, creating an X, Y array in a 2D space (with dimension = webcam max dimensions). This array serves as a crucial piece of data that acts as the foundation for the subsequent transformation into a three-dimensional representation. By mapping this 2D body position to the vast expanse of a virtual 3D space, the app enables an unparalleled sense of immersion by precisely pinpointing the location of the user's head within the living room. Through this extraordinary combination of cutting-edge technology, the app takes home entertainment experiences to an entirely new dimension, blurring the boundaries between the real and virtual worlds.

Once the 2D body position is translated into a 3D representation, the app seamlessly transfers the data to a rendering software - in my case leveraging an existing game design framework (Unity and C#). Unity takes the 3D points representing the location of the user's head in the living room and utilizes them to recreate an immersive 3D environment. It meticulously constructs a virtual space that mirrors your physical surroundings, accounting for depth, perspective, and spatial relationships. With careful attention to detail, the software renders this virtual environment, transforming it into a captivating visual display that can be seamlessly projected onto your living room TV. As a result, the user is transported into a mesmerizing realm where the boundaries between reality and the virtual world dissolve, providing an unparalleled and truly immersive home entertainment experience.

## Conclusion 

Now, as I sit in awe of my living room, I am thrilled to share the fruits of my labor. What was once a conventional flat screen has been transformed into a breathtaking 3D display, capable of projecting immersive visuals that seem to leap out of the screen and into my living space. Whether I'm watching a thrilling action movie, exploring virtual worlds, or simply enjoying the beauty of nature documentaries, the newfound depth and realism of the visuals have redefined my home entertainment experience.
