---
layout: page
title: Satisfying contraints using noisy features
description: Assembling a unicolor puzzle using computer vision
img: assets/img/puzzle_final/thumbnail.jpg
importance: 1
category: fun
---

I explore the problem of large space constraints satisfaction with mininal, noisy information - a topic important in Operations Research, Scheduling, etc. To test some ideas on an application, I have decided to build a program that uses computer vision techniques to solve jigsaw puzzles using only the shapes of the edges. I have also always thought it would be very cool to display a single color Jigsaw puzzle.
You can find the code repository [on Github](https://github.com/eyast/PuzzleGenerator), or if you feel like solving this problem your own way, you can find the dataset (scanned copies of the jigsaw puzzle pieces, raw) [here](https://www.kaggle.com/datasets/etaifour/jigsawpuzzle).

## Data Provenance
### Sourcing

I've decided to purchase a 2000 pieces custom Jigsaw puzzle, from the first online vendor I could find who could provide 2000 pieces. They had good ratings online, and they provided a nice web page that allowed me to upload a custom photo and create my custom puzzle. The tool allowed me to pick a custom background color (probably to fill in the empty areas left around the uploaded image). I've use this GUI to select a green-ish color, and proceeded to payment, upon which their customer care department double-checked with me to make sure that I haven't made a mistake.

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
7. Turn the lights off
8. Scan the pieces
9. Store them in the appropriate drawer

<div class="row">
    <div class="col-sm mt-3 mt-md-0">
        {% include figure.html path="assets/img/puzzle_final/01_left_box_drawers.jpg" title="I bought a drawer with 20 compartments from my local DIY store." class="img-fluid rounded z-depth-1" %}
    </div>
    <div class="col-sm mt-3 mt-md-0">
        {% include figure.html path="assets/img/3.jpg" title="example image" class="img-fluid rounded z-depth-1" %}
    </div>
    <div class="col-sm mt-3 mt-md-0">
        {% include figure.html path="assets/img/5.jpg" title="example image" class="img-fluid rounded z-depth-1" %}
    </div>
</div>
<div class="caption">
    I organized the physical pieces in such a way they were retrievable, and their orientiation would be known.
</div>

PuzzleSolver 2019 extends Joe Zeimen's original work from 2013. It is a hybrid command-line/GUI application -- input parameters are specified on the command line, status and results are written to the console and to various files in the output directory. There are times when PuzzleSolver will pupup a GUI window -- namely when verifying contours found in the input images, when allowing the user to manually adjust the corner locations of a piece, and in guided solution mode to present suggested piece matches. If a solution can be found, then an image of the solved puzzle is shown in a GUI window.

To give your project a background in the portfolio page, just add the img tag to the front matter like so:

    ---
    layout: page
    title: project
    description: a project with a background image
    img: /assets/img/12.jpg
    ---

<div class="row">
    <div class="col-sm mt-3 mt-md-0">
        {% include figure.html path="assets/img/1.jpg" title="example image" class="img-fluid rounded z-depth-1" %}
    </div>
    <div class="col-sm mt-3 mt-md-0">
        {% include figure.html path="assets/img/3.jpg" title="example image" class="img-fluid rounded z-depth-1" %}
    </div>
    <div class="col-sm mt-3 mt-md-0">
        {% include figure.html path="assets/img/5.jpg" title="example image" class="img-fluid rounded z-depth-1" %}
    </div>
</div>
<div class="caption">
    Caption photos easily. On the left, a road goes through a tunnel. Middle, leaves artistically fall in a hipster photoshoot. Right, in another hipster photoshoot, a lumberjack grasps a handful of pine needles.
</div>
<div class="row">
    <div class="col-sm mt-3 mt-md-0">
        {% include figure.html path="assets/img/5.jpg" title="example image" class="img-fluid rounded z-depth-1" %}
    </div>
</div>
<div class="caption">
    This image can also have a caption. It's like magic.
</div>

You can also put regular text between your rows of images.
Say you wanted to write a little bit about your project before you posted the rest of the images.
You describe how you toiled, sweated, *bled* for your project, and then... you reveal its glory in the next row of images.


<div class="row justify-content-sm-center">
    <div class="col-sm-8 mt-3 mt-md-0">
        {% include figure.html path="assets/img/6.jpg" title="example image" class="img-fluid rounded z-depth-1" %}
    </div>
    <div class="col-sm-4 mt-3 mt-md-0">
        {% include figure.html path="assets/img/11.jpg" title="example image" class="img-fluid rounded z-depth-1" %}
    </div>
</div>
<div class="caption">
    You can also have artistically styled 2/3 + 1/3 images, like these.
</div>


The code is simple.
Just wrap your images with `<div class="col-sm">` and place them inside `<div class="row">` (read more about the <a href="https://getbootstrap.com/docs/4.4/layout/grid/">Bootstrap Grid</a> system).
To make images responsive, add `img-fluid` class to each; for rounded corners and shadows use `rounded` and `z-depth-1` classes.
Here's the code for the last row of images above:

{% raw %}
```html
<div class="row justify-content-sm-center">
    <div class="col-sm-8 mt-3 mt-md-0">
        {% include figure.html path="assets/img/6.jpg" title="example image" class="img-fluid rounded z-depth-1" %}
    </div>
    <div class="col-sm-4 mt-3 mt-md-0">
        {% include figure.html path="assets/img/11.jpg" title="example image" class="img-fluid rounded z-depth-1" %}
    </div>
</div>
```
{% endraw %}
