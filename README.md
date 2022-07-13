# ConvNext-ROS2 Wrapper

This is a ROS2 wrapper for the (A ConvNet for the 2020s) Convolutional Neural Network inspired from Vision Transformers, [ConvNext](https://arxiv.org/abs/2201.03545). We utilize `huggingface` and the `transformers` for the [source of the algorithm](https://huggingface.co/facebook/convnext-tiny-224). The main idea is for this container to act as a standalone interface and node, removing the necessity to integrate separate packages and solve numerous dependency issues. We have built all different versions of the algorithm (i.e. `base-patch16-224`, `large-patch16-224`, etc.) for each ROS2 distribution.

The ConvNet paper gradually "modernizes" a standard ResNet toward the design of a Vision Transformer, and discovers several key components that contribute to the performance difference along the way. More can be found in the [paper](https://arxiv.org/abs/2201.03545)

# Installation Guide

## Using Docker Pull
1. Install [Docker](https://www.docker.com/) and ensure the Docker daemon is running in the background.
2. Run ```docker pull shaderobotics/swin:${ROS2_DISTRO}-${MODEL_VERSION}``` we support all ROS2 distributions along with all model versions found in the model version section below.
3. Follow the run commands in the usage section below

## Build Docker Image Natively
1. Install [Docker](https://www.docker.com/) and ensure the Docker daemon is running in the background.
2. Clone this repo with ```git pull https://github.com/open-shade/swin.git```
3. Enter the repo with ```cd swin```
4. To pick a specific model version, edit the `ALGO_VERSION` constant in `/swin/swin.py`
5. Build the container with ```docker build . -t [name]```. This will take a while. We have also provided associated `cloudbuild.sh` scripts to build on GCP all of the associated versions.
6. Follow the run commands in the usage section below.

# Model Versions

* ```base-224```
* ```tiny-224```
* ```small-224```
* ```large-224```
* ```base-384```
* ```large-384```

More information about these versions can be found in the [paper](https://arxiv.org/abs/2010.11929). `base`, `large`, represent the number of weights stored (i.e. the size of the model). 384 is the resolution size of the image. 

## Example Docker Command

```bash
docker pull shaderobotics/convnext:foxy-base-224
```

# Usage
## Run the SWIN Node 
Run ```docker run --net=host shaderobotics/convnext:${ROS_DISTRO}-${MODEL_VERSION}```. Your node should be running now. Then, by running ```ros2 topic list,``` you should see all the possible pub and sub routes.

For more details explaining how to run Docker images, visit the official Docker documentation [here](https://docs.docker.com/engine/reference/run/). Also, additional information as to how ROS2 communicates between external environment or multiple docker containers, visit the official ROS2 (foxy) docs [here](https://docs.ros.org/en/foxy/How-To-Guides/Run-2-nodes-in-single-or-separate-docker-containers.html#). 

# Topics

| Name                   | IO  | Type                             | Use                                                               |
|------------------------|-----|----------------------------------|-------------------------------------------------------------------|
| convnext/image_raw       | sub | [sensor_msgs.msg.Image](http://docs.ros.org/en/noetic/api/sensor_msgs/html/msg/Image.html)            | Takes the raw camera output to be processed                       |
 | convnext/result           | pub | String            | Outputs the classification label from ImageNet 100 Classes as a string |

# Testing / Demo
To test and ensure that this package is properly installed, replace the Dockerfile in the root of this repo with what exists in the demo folder. Installed in the demo image contains a [camera stream emulator](https://github.com/klintan/ros2_video_streamer) by [klintan](https://github.com/klintan) which directly pubs images to the ConvNext node and processes it for you to observe the outputs.

To run this, run ```docker build . -t [name]```, then ```docker run --net=host -t [name]```. Observing the logs for this will show you what is occuring within the container. If you wish to enter the running container and preform other activities, run ```docker ps```, find the id of the running container, then run ```docker exec -it [containerId] /bin/bash```