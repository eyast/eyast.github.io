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

PS: Co-authored by ChatGPT.

# Project Description

In the midst of the global pandemic, most of us found ourselves adjusting to a new normal – working from the comfort of our homes. As the days blurred together and time seemed to stretch endlessly, I, like many others, discovered that this newfound freedom came with ample opportunities for exploration and creativity. With extra time on my hands and a burning desire for a captivating home entertainment experience, I embarked on an exciting and ambitious project: transforming my living room TV into a mesmerizing 3D display with perspective-corrected visuals.

The idea struck me one evening as I was working from my living room, staring at the turned off TV. While my television offered hours of entertainment, I craved something more immersive – an experience that would transport me into the heart of the action, making me feel like a participant rather than a mere observer. With a passion for technology and a love for pushing boundaries, I set out to achieve the impossible within the confines of my own living room.

With a clear vision in mind, I dove headfirst into research, exploring technologies and existing DIY projects that hinted at the possibility of transforming a regular TV into a 3D display. I discovered that the key lied in leveraging the power of perspective correction – a technique that adjusts the projected image to match the viewer's position, creating a compelling illusion of depth and dimension. 

Armed with knowledge, determination, and an assortment of tools, I began the intricate process of modifying my television. It involved a combination of hardware modifications, software tweaks, and a fair share of trial and error. As I delved deeper into the project, I found myself facing technical challenges and embracing the learning curve that came with venturing into uncharted territory. It was an exhilarating journey that tested my problem-solving skills and ignited my passion for pushing the boundaries of what was possible.


# Solution Overview

The core of this innovative app revolves around harnessing the power of computer vision and utilizing a webcam to capture an image, subsequently analyzing it to determine the presence of a human. By leveraging PoseNet and a custom Neural Network built for purpose, the solution can swiftly identify and isolate the human figure within the image. Once the human presence is confirmed, the app goes a step further by extracting the body position, creating an X, Y array in a 2D space (with dimension = webcam max dimensions). This array serves as a crucial piece of data that acts as the foundation for the subsequent transformation into a three-dimensional representation. By mapping this 2D body position to the vast expanse of a virtual 3D space, the app enables an unparalleled sense of immersion by precisely pinpointing the location of the user's head within the living room. Through this extraordinary combination of cutting-edge technology, the app takes home entertainment experiences to an entirely new dimension, blurring the boundaries between the real and virtual worlds.

Once the 2D body position is translated into a 3D representation, the app seamlessly transfers the data to a rendering software - in my case leveraging an existing game design framework (Unity and C#). Unity takes the 3D points representing the location of the user's head in the living room and utilizes them to recreate an immersive 3D environment. It meticulously constructs a virtual space that mirrors your physical surroundings, accounting for depth, perspective, and spatial relationships. With careful attention to detail, the software renders this virtual environment, transforming it into a captivating visual display that can be seamlessly projected onto your living room TV. As a result, the user is transported into a mesmerizing realm where the boundaries between reality and the virtual world dissolve, providing an unparalleled and truly immersive home entertainment experience.

## Components

To ensure a streamlined and secure setup, the solution incorporates 3D printed parts that play a crucial role in organizing and securing the components. These custom-designed parts are instrumental in creating a tidy and fixed arrangement, ensuring that each element fits seamlessly into the overall structure. The camera, a vital component of the system, is securely held in place, guaranteeing its stability and eliminating any unintended movements during operation.

To handle the processing and inference tasks, the solution utilizes a  Nvidia Jetson Nano embedded platform. Running within a Docker container, the Nano hosts a FastAPI server, serving inference capabilities as a REST API. This setup enables seamless communication and interaction with other components of the system. Unity, a popular game development platform, leverages the REST API to access the inference results, allowing for the integration of the captured 3D body position data into the virtual environment.

The decision to opt for the Nvidia Jetson Nano was driven by the desire to leverage its onboard processing capabilities for running neural networks. This not only streamlines the overall setup but also enhances the system's efficiency by offloading the processing workload from external devices. Additionally, the Nano's versatility allows it to serve a dual purpose by utilizing the same device for rendering the scene on the TV. This integration further enhances the synchronization and fluidity of the overall experience, providing a seamless transition between the captured 3D environment and its projection on the television screen.

### 3D parts

When it came to the 3D component, I enthusiastically delved into the creative process, utilizing the powerful design capabilities of Fusion. Through several iterations and countless hours of refining, I meticulously crafted a bespoke object tailored to perfection. This custom creation serves as the ideal support, elegantly cradling the camera in a secure and immovable position. From conception to execution, the journey of bringing this custom object to life has been a true labor of love, ensuring that every detail is carefully considered to deliver a seamless and captivating experience.

<div class="row">
    <div class="col-sm mt-3 mt-md-0">
        {% include figure.html path="assets/img/puzzle_final/01_left_box_drawers.jpg" title="I bought a drawer with 20 compartments from my local DIY store." class="img-fluid rounded z-depth-1" %}
    </div>
    <div class="col-sm mt-3 mt-md-0">
        {% include figure.html path="assets/img/puzzle_final/02_scanning.jpg" title="scanning the pieces on a flatbed scanner, in a dark room." class="img-fluid rounded z-depth-1" %}
    </div>
</div>
<div class="caption">
    Chassis for the Logicom C920 webcam. Fusion render and 3D print displayed.
</div>


# Conclusion 

Now, as I sit in awe of my living room, I am thrilled to share the fruits of my labor. What was once a conventional flat screen has been transformed into a breathtaking 3D display, capable of projecting immersive visuals that seem to leap out of the screen and into my living space. Whether I'm watching a thrilling action movie, exploring virtual worlds, or simply enjoying the beauty of nature documentaries, the newfound depth and realism of the visuals have redefined my home entertainment experience.
