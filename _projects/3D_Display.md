---
layout: page
title: Mind Blowing 3D display at home
description: Transforming my Living Room TV into a Mind-blowing 3D
img: assets/img/puzzle_final/thumbnail.jpg
importance: 1
category: fun
---

<div class="row">
    <div class="col-sm mt-3 mt-md-0">
        {% include figure.html path="assets/img/puzzle_final/final.jpg" title="Custom-made, forced perspective renderer" class="img-fluid rounded z-depth-1" %}
    </div>
</div>
<div class="caption">
    A custom made software analyzes my location, and renders a 3D scene epxplicitly for my viewing angle.
</div>

PS: Co-authored by ChatGPT (hence, very wordy).

# Project Description

Amidst the pandemic, many of us adjusted to working from home. In search of a captivating home entertainment experience, I transformed my living room TV into a mesmerizing 3D display. Inspired by the desire for immersion, I started exploring the world of real-time perspective correction. I built an app that tracks me in my living room, and accordingly renders a 3D scene on my TV, for my own viewing, with the correct perspective that matches my position to create an optical illusion. Basically, an exercise of building a real-time [forced perspective](https://en.wikipedia.org/wiki/Forced_perspective) mechanism.

The app uses computer vision and a webcam to detect humans, isolate their figures, and extract body positions in a 2D space. This data is then transformed into a three-dimensional representation of the user's position in the room. By mapping the 2D body position to a virtual 3D space, the app provides an immersive experience by accurately locating the user's head within the living room. It combines advanced technology to push the boundaries between the real and virtual worlds, enhancing home entertainment experiences.

Once the 2D body position is translated into a 3D representation, a game rendering engine (Unity) takes the 3D points representing the location of the user's head in the living room and utilizes them to recreate an immersive 3D environment. It meticulously constructs a virtual space that mirrors your physical surroundings, accounting for depth, perspective, and spatial relationships. With careful attention to detail, the software renders this virtual environment, transforming it into a captivating visual display that can be seamlessly projected onto your living room TV. As a result, the user is transported into a mesmerizing realm where the boundaries between reality and the virtual world dissolve, providing an unparalleled and truly immersive home entertainment experience.

From a high level perspective, the application is composed of two executables: 
1. A docker container running on a [Nvidia Jetson Nano](https://www.nvidia.com/en-au/autonomous-machines/embedded-systems/jetson-nano/) that infers the viewer's position in my living room, and serves that to a downstream client as a JSON reply. 
2. The  client (Unity in this case), queries the API once per Frame Refresh, and uses this information to modify the Scene's. A 3D object is placed behind the TV, and is rendered.

THe diagram below shows the different components at work, at every single Frame refresh. The Jetson Nano includes the following sub-components:
1. A webcam - a [Logicom c920 webcam](https://www.logitech.com/en-au/products/webcams/c920-pro-hd-webcam.960-000770.html).
2. PoseNet - [a built-in Neural Network](https://github.com/dusty-nv/jetson-inference/blob/master/docs/posenet.md) that takes in an image, and returns the location of BodyPose KeyPoints.
3. A custom Features Creator moduled (detailed below). The FeaturesCreator transforms the raw data returned by PoseNet to features I use in my application.
4. A custom Neural Network, that transforms 2D pixel values of the previous steps, into 3D vector space mapped to my living room.
5. [A Kalman filter](https://en.wikipedia.org/wiki/Kalman_filter), which I use to reduce the noise returned from my Neural Network.
6. [FastAPI](https://fastapi.tiangolo.com/), which provides a JSON answer to anyone who access / on HTTP. 
7. [A Unity](https://unity.com/pages/unity-pro-buy-now?utm_source=google&utm_medium=cpc&utm_campaign=cc_dd_upr_sapac_sapac-t1_en_pu_sem-gg_acq_br-pr_2023-01_brand-st1_cc3022_ev-br_id:71700000106719832&utm_content=cc_dd_upr_apac_pu_sem_gg_ev-br_pros_x_npd_cpc_kw_sd_all_x_x_brand_id:58700008276350171&utm_term=unity&&&&&gad=1&gclid=Cj0KCQjwj_ajBhCqARIsAA37s0wD9evaH3lHrzZ4UXZYJU8WgJ9Jt39LSVC7Vnx_l_goCvR6FmMKfTUaArlyEALw_wcB&gclsrc=aw.ds) client, in which the Camera's Position is set to what the FastAPI endpoint returned.


{% mermaid %}
sequenceDiagram
    participant Webcam
    participant PoseNet
    participant FeatureCreator
    participant CustomNet
    participant KalmanFilter
    participant FastAPI
    participant Unity
    loop everyFrame
    Unity->>FastAPI:If there's a human in the Frame, what's the position of their head in 3D space?
    FastAPI->>+Webcam: Take an image
    Webcam->>-FastAPI: Image returned.
    FastAPI->>+PoseNet: Where are the Body Pose KeyPoints?
    PoseNet->>-FastAPI: Raw_data returned.
    FastAPI->>+FeatureCreator: What are the features in this raw data point?
    FeatureCreator->>-FastAPI: Features returned.
    FastAPI->>+CustomNet: Here's a list of features - Infer the 3D location of the camera/head in the room
    CustomNet->>-FastAPI: Location in 3D space returned.
    FastAPI->>+KalmanFilter: Filter this noisy data
    KalmanFilter->>-FastAPI: Filtered location returned.
    FastAPI->>Unity: 3D location of camera returned in JSON.
    Unity->>Unity: Modify the scene according to 3D location.
    Unity->>Unity: Render the scene.
    end

{% endmermaid %}

## Components

To ensure a streamlined and secure setup, the solution incorporates 3D printed parts that play a crucial role in organizing and securing the components. These custom-designed parts are instrumental in creating a tidy and fixed arrangement, ensuring that each element fits seamlessly into the overall structure. The camera, a vital component of the system, is securely held in place, guaranteeing its stability and eliminating any unintended movements during operation.

To handle the processing and inference tasks, the solution utilizes a  Nvidia Jetson Nano embedded platform. Running within a Docker container, the Nano hosts a FastAPI server, serving inference capabilities as a REST API. This setup enables seamless communication and interaction with other components of the system. Unity, a popular game development platform, leverages the REST API to access the inference results, allowing for the integration of the captured 3D body position data into the virtual environment.

The decision to opt for the Nvidia Jetson Nano was driven by the desire to leverage its onboard processing capabilities for running neural networks. This not only streamlines the overall setup but also enhances the system's efficiency by offloading the processing workload from external devices. Additionally, the Nano's versatility allows it to serve a dual purpose by utilizing the same device for rendering the scene on the TV. This integration further enhances the synchronization and fluidity of the overall experience, providing a seamless transition between the captured 3D environment and its projection on the television screen.

### 3D parts

When it came to the 3D component, I enthusiastically delved into the creative process, utilizing the powerful design capabilities of Fusion. Through several iterations and countless hours of refining, I meticulously crafted a bespoke object tailored to perfection. This custom creation serves as the ideal support, elegantly cradling the camera in a secure and immovable position. From conception to execution, the journey of bringing this custom object to life has been a true labor of love, ensuring that every detail is carefully considered to deliver a seamless and captivating experience.

<div class="row">
    <div class="col-sm mt-3 mt-md-0">
        {% include figure.html path="assets/img/3ddisplay/fusion_cam.jpg" title="Model I created in Fusion to hold the camera steadily on top of the TV." class="img-fluid rounded z-depth-1" %}
    </div>
    <div class="col-sm mt-3 mt-md-0">
        {% include figure.html path="assets/img/3ddisplay/cam_printed.jpg" title="Printed on Creality S3 Pro v1." class="img-fluid rounded z-depth-1" %}
    </div>
</div>
<div class="caption">
    Chassis for the Logicom C920 webcam. Fusion render and 3D print displayed.
</div>

### Building for the Jeston Nano

A note on getting the correct power supply and storage: The Nvidia Jetson Nano relies on a 5V 2A power supply to ensure it receives sufficient power to drive its GPU effectively. Without a suitable power supply, the Nano will decrease the power supply to the GPU, leading to a suboptimal user experience characterized by choppy performance. It is crucial to provide the Nano with the recommended power specifications to enable smooth and uninterrupted GPU functionality.
In addition to the power supply requirements, it is important to note that the storage SD card for the Nvidia Jetson Nano should not only have a large capacity, preferably above 32GB, but also be fast. The choice of a fast SD card is crucial as it directly impacts the inference time of the system. A high-speed SD card enables quicker read and write operations, facilitating faster data access and processing, ultimately reducing the time it takes for the Nano to perform inference tasks. Therefore, selecting a storage SD card that combines ample capacity with fast transfer speeds is essential for optimizing the overall performance of the Jetson Nano.

#### NVidia Hello World repository
I built my work on the excellent Hello World Nvidia [repository](https://github.com/dusty-nv/jetson-inference). The very first thing I did was a smoke test: I ran one of the built-in networks to validate that it works, eager to witness the possibilities firsthand. With anticipation, I cloned the "hello world" repository, embracing the excitement of diving into uncharted territory. Running one of the built-in networks, PoseNet, I eagerly awaited the results, breathless with anticipation. As the virtual scene unfolded before my eyes, a surge of wonder and amazement engulfed me. The captivating visualization offered a mere glimpse into the immense potential of this project, fueling my drive to explore further and push the boundaries of what could be achieved. This initial foray served as a powerful catalyst, propelling me into an enchanting realm of immersive possibilities that would redefine my home entertainment experience.

<div class="row">
    <div class="col-sm mt-3 mt-md-0">
        {% include figure.html path="assets/img/3ddisplay/PoseNet.jpg" title="PoseNet" class="img-fluid rounded z-depth-1" %}
    </div>
</div>
<div class="caption">
    Running out-of-the-box PoseNet to see what the Nvidia Jetaon Nano can do. Pretty cool.
</div>

#### Setting up SSH for vscode remote + github auth

WIP.

#### Creating a new DockerFile to install FastAPI instead of Flask

To streamline the deployment process, I developed a customized Dockerfile based on the original Nvidia image, incorporating essential modifications and installing FastAPI. While I initially aimed to leverage VSCode DevContainers for seamless development, I encountered limitations due to the unsupported Python version within the image. However, this obstacle became an opportunity for growth as I delved into the intricacies of debugging, mastering the usage of pdb.set_trace() and remote debugging. This exercise not only enhanced my troubleshooting skills but also equipped me with invaluable insights into optimizing and fine-tuning the software environment for optimal performance.

#### Collecting training data

With the Docker container up and running, the next step in my journey is to focus on building the application that will serve as the foundation for collecting crucial data – training and evaluation data for downstream applications. This data will play a pivotal role in constructing a network that takes a set of detected Body Pose KeyPoints as input and outputs precise x, y, and z coordinates representing the viewer's head. Given that the application is designed solely for my personal use, I embrace the freedom to overfit and lock the z view to a fixed height of 190 cm, aligning with my own height. This tailored approach ensures a customized and accurate representation, elevating the immersive experience to new heights.

The application I have developed is a user-friendly FastAPI form that collects my physical X and Y coordinates relative to a fictional (0, 0) point, representing the corner of the room. Upon clicking a button on the web page, the Docker container springs into action, capturing a series of 50 consecutive images. It then proceeds to extract the body pose keypoints from each image and calculates the corresponding features. These calculated features are then appended to a CSV file, with each row augmented by ground truth label values that represent my actual physical position within the room. This comprehensive data collection process enables the creation of a robust dataset, essential for training and refining downstream applications that rely on accurate viewer positioning.

The first step was to create a grid that includes 1x1 squares, where the grid's point of origin is the same as the room's origin. I used good old tape for this practice. At a later stage, I added markers every 0.5 meters, to increase the capabilities of the Neural Network.

<div class="row">
    <div class="col-sm mt-3 mt-md-0">
        {% include figure.html path="assets/img/3ddisplay/floor_markers.jpg" title="I put scotch tape markers 1 meter apart in my living room." class="img-fluid rounded z-depth-1" %}
    </div>
</div>
<div class="caption">
    Markers on the floor of the living room, 1 and 0.5 meter apart. These markers let me know where to stand when capturing training data.
</div>

To gather diverse and generalized data specific to myself, I adopted a comprehensive approach. I consciously varied my attire throughout the day, accounting for different lighting conditions as well. While capturing the images, I made an effort to maintain a steady posture; however, I allowed myself some leeway to enjoy music, which may have resulted in slight head movements. This meticulous process was my attempt to collect a broad range of data points, conditioned to a single individual—myself. By incorporating these variations, the dataset encompasses a realistic representation of different scenarios and conditions, enabling the network to generalize and adapt to varying circumstances.

<div class="row">
    <div class="col-sm mt-3 mt-md-0">
        {% include figure.html path="assets/img/3ddisplay/RoomLayout.jpg" title="Origin Point in the room" class="img-fluid rounded z-depth-1" %}
    </div>
</div>
<div class="caption">
    I set the room corner as the origin point - in retrospect, I should have used the Webcam as the origin point to make my code more reusable. This diagram shows a sample spot in the room and how that location is interpreted.
</div>

#### Calculating features

To extract meaningful features for my network, I leveraged the 18 Keypoints generated by PoseNet. Focusing on symmetric keypoints, which have corresponding "left" and "right" components (such as left_eye, right_eye, left_shoulder, right_shoulder, etc.), I computed three crucial metrics for each. Firstly, I calculated the pixel distance between the left and right keypoints, providing insights into the relative spatial positioning. Secondly, I determined the center location of each symmetric keypoint, offering a reference point for further analysis. Lastly, I derived the angular tilt of each symmetric keypoint, enabling a deeper understanding of the body pose and its orientation. By incorporating these calculated features, my network gains a comprehensive understanding of the viewer's body positioning, enhancing the accuracy and realism of the 3D display experience.

<div class="row">
    <div class="col-sm mt-3 mt-md-0">
        {% include figure.html path="assets/img/3ddisplay/features_table.jpg" title="Raw collected data" class="img-fluid rounded z-depth-1" %}
    </div>
</div>
<div class="caption">
    The raw data: The first 28 columns point to the X, Y location of each individual Body Pose KeyPoint (nose and left eye only shown). The last 2 columns show ground truths.
</div>

The features table serves as a structured repository for the calculated features, each measured in absolute pixel values within the permissible range of the webcam. Each row within the table concludes with two ground values representing my precise X and Y position in the physical room. This feature-to-label mapping forms the foundation for training a neural network capable of accurately mapping the 30 extracted features to just two output values. Prior to feeding the data into the neural network, a linear scaling process is applied, transforming the feature values to a normalized range between 0 and 1, and transforming ground values on a scale of [0, 8] and [0, 6] for my X, Y values respectively (the range is based on my living room size, measures in meters). This scaling ensures consistency and optimal performance during the training process, setting the stage for the network to learn and predict the viewer's physical position with enhanced precision.


#### Building a custom Neural Network to map 2d to 3D

WIP.

#### Overfitting and experimenting with different network architectures

WIP.

#### Understanding the infered distribution in comparison to ground truths

WIP.

#### Building a Kalman Filter with Copilot

WIP.

#### Putting this to the test on an inference point

WIP.

#### Creating a decoupled streaming architecture (latency problem)

WIP.


# Conclusion 

Now, as I sit in awe of my living room, I am thrilled to share the fruits of my labor. What was once a conventional flat screen has been transformed into a breathtaking 3D display, capable of projecting immersive visuals that seem to leap out of the screen and into my living space. Whether I'm watching a thrilling action movie, exploring virtual worlds, or simply enjoying the beauty of nature documentaries, the newfound depth and realism of the visuals have redefined my home entertainment experience.
