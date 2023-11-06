# MLOPS project 2

This repository completes the fourth task in the second project of the subject MLOPS at HSLU in semester HS23. The repository contains all the project files including the Dockerfiles and describes all necessary steps to run the docker file on your own devices.

## Project Description

The goal of this project is to run a training of a predefined model in a Docker container. The [code](https://colab.research.google.com/drive/1J9YCDwPIzzz_d_dqasjWHzr7_meK-3M7?usp=sharing) without any logging or optimized parameters was given to us. The task of the first project was to optimize the hyperparameters. In this project we needed to use these optimized hyperparameters and log the training results with a library of our choice.

## Build Docker images

To build the docker images all the files and instructions are in the folder [build_docker_images](build_docker_images). There are two requirements needed to create the image. Firstly you need to have docker installed on your target device, else you can't build or run a docker image. Secondly you need to have a weights and biases account. The reason for that is because the report will be generated in weights and biases. All further instructions can be found in the folder.

## Copy Docker images

If you don't want to build the Docker images yourself, you can just download my prebuild images that you can find in the folder [docker_images](docker_images). Two images have been uploaded, one with CUDA installed and one without. The version without CUDA installed has a smaller file size of 2.5 GB.  
The main disadvantage of using those images is that you can't get access to the report because it sends the report to my weights and biases account.
To run the container, read the instructions in the [build_docker_images](build_docker_images) folder from the point [run](build_docker_images/readme.md#run-container) container.
