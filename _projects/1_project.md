---
layout: page
title: Satisfying contraints using noisy features
description: Assembling a unicolor puzzle using computer vision
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

I explore the problem of large space constraints satisfaction with mininal, noisy information - a topic important in Operations Research, Scheduling, etc. To test some ideas on an application, I have decided to build a program that uses computer vision techniques to solve jigsaw puzzles using only the shapes of the edges. I have also always thought it would be very cool to display a single color Jigsaw puzzle.
You can find the code repository [on Github](https://github.com/eyast/PuzzleGenerator), or if you feel like solving this problem your own way, you can find the dataset (scanned copies of the jigsaw puzzle pieces, raw) [here](https://www.kaggle.com/datasets/etaifour/jigsawpuzzle).

## Data Provenance
### Sourcing

I've decided to purchase a 2000 pieces custom Jigsaw puzzle, from the first online vendor I could find who could provide that size. They had good ratings online, and they provided a nice web page tool which allowed me to pick a custom background color and add a photo. Since my customisations were very scarce, their customer care department reached out to make sure that I haven't made a mistake.

<div class="row">
    <div class="col-sm mt-3 mt-md-0">
        {% include figure.html path="assets/img/puzzle_final/email_screenshot.jpg" title="Concerned customer care = good customer care" class="img-fluid rounded z-depth-1" %}
    </div>
</div>
<div class="caption">
    The people at PuzzlesPrint were concerned. That's good customer care.
</div>

### Indexing, Digitizing
I was unaware if those pieces were unique or not, or if they were correctly assemblable. After a bit of pondering, I've decided to index, scan, and organize these pieces. The process was:
1. Buy a 0.2 mm pen
2. Buy a 20 drawers cabinet from Bunnings
3. Add a little sticker to each drawer of that cabinet, starting at 1:100, 101:200, 201:300, etc..
4. Number each piece sequentially, starting from 1
5. For each numbered piece, add a horizontal line above. This would then help define the baseline orientiation of the piece, and help resolve future problems (i.e. am I looking at a '9' or an inverting '6')
6. Once all the pieces were numbered, put a batch of ~50 aligned pieces on the flatbed scanner
7. Turn the lights off - you want to only rely on the flatbed scanner's light source and not the ambient light in the room, since you only want the surface to be scanned (and not the depth of each puzzle piece)
8. Scan the pieces - single channel BW
9. Store the physical pieces in the appropriate drawer

<div class="row">
    <div class="col-sm mt-3 mt-md-0">
        {% include figure.html path="assets/img/puzzle_final/01_left_box_drawers.jpg" title="I bought a drawer with 20 compartments from my local DIY store." class="img-fluid rounded z-depth-1" %}
    </div>
    <div class="col-sm mt-3 mt-md-0">
        {% include figure.html path="assets/img/puzzle_final/02_scanning.jpg" title="scanning the pieces on a flatbed scanner, in a dark room." class="img-fluid rounded z-depth-1" %}
    </div>
    <div class="col-sm mt-3 mt-md-0">
        {% include figure.html path="assets/img/puzzle_final/03_results.jpg" title="The results of the scan." class="img-fluid rounded z-depth-1" %}
    </div>
</div>
<div class="caption">
    I organized the physical pieces in such a way they were retrievable, and their orientiation would be known. Having 2000 pieces spread in 20 drawers made me appreciate the 'size' and 'size on disk' properties of a file system - but that's another story for another day.
</div>

With this done I now had few JPGs of the 2000 pieces, and my task now was to identify the pieces, and 'glue them' together.

<div class="row">
    <div class="col-sm mt-3 mt-md-0">
        {% include figure.html path="assets/img/puzzle_final/04_scanned_results.jpg" title="scanned results" class="img-fluid rounded z-depth-1" %}
    </div>
</div>


## Experimenting
### 1. Finding pieces on a sheet of paper

As soon as I was done from the tedious task of indexing, scanning, and storing all those pieces, it was time to start the real work. My thought process at this stage was not very clear and I knew the first step I had to do was to find the puzzle pieces. To do this, I opted to leverage OpenCV's existing libraries.

This included at the very beginning, blurring each image to remove unnecessary noise. And then using the threshold function to draw. Explicit. Black and white areas that represent pieces of puzzles versus background.I had originally indexed each piece by writing its sequence number. Therefore this created a lot of connected components in open CV. To be able to differentiate the pieces from the noise, I decided to use Scikit learn K-means Clustering function with two clusters centroids:Large clusters versus the noisy small clusters.