<h1 align="center">
  <a href="https://docker.com">
    <img alt="spaceship →~ prompt" src="https://www.docker.com/sites/default/files/social/docker_facebook_share.png" width="400">
  </a>
  <br>A Quickstart Tutorial<br>
</h1>

Compiled by Manu Suresh, Aakash Patel, Chris Raitano, Cameron Blumenthal, Eric Leyda, and Alec Stewart

## Introduction
### What is Docker?

[Docker](https://docker.com) is a tool designed to create and run applications through the use of containers. Simply put, you can package up your entire application and ship it as one complete unit with libraries, dependencies, and anything else your app specifically needs. The convenience of Docker is that it can run on any machine where Docker is installed, regardless of the configuration of the kernel!

### What Docker version should I use?
For this tutorial, we are using the [Docker Community Edition (CE)](https://docs.docker.com/install/overview/). Docker mentions that it is "ideal for individual developers and small teams looking to get started with Docker and experimenting with container-based apps."

## Step 1: Installing Docker

For the purposes of this tutorial, we will be installing and running Docker on Ubuntu 18.04.

### Setup the Docker repository

1. Update the `apt` package index:

    ```
    sudo apt update
    ```
   
2. Install necessary packages:

    ```
    sudo apt-get install \
        apt-transport-https \
        ca-certificates \
        curl \
        gnupg-agent \
        software-properties-common
    ```
    
3. Add Docker's official GPG key:

    ```
    curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo apt-key add -
    ```
    
    And then verify you have the key with the fingerprint `9DC8 5822 9FC7 DD38 854A E2D8 8D81 803C 0EBF CD88`:
    
    ```
    sudo apt-key fingerprint 0EBFCD88
    ```
    
4. Add the `stable` repository:

    ```
    sudo add-apt-repository \
        "deb [arch=amd64] https://download.docker.com/linux/ubuntu \
        $(lsb_release -cs) \
        stable"
    ```
    
### Install Docker CE

1. Update the `apt` package index one more time:

    ```
    sudo apt update
    ```
    
2. Install the latest version of Docker CE:

    ```
    sudo apt-get install docker-ce docker-ce-cli containerd.io
    ```
    
3. Verify Docker CE is installed and running:

    ```
    sudo docker run hello-world
    ```
    
Congratulations! You have installed Docker. Now on to the good stuff.

## Step 2: Creating a Container

The first order of business is deciding what goes on in the environment of the Docker container we are about to create. As a side note, keep in mind that any given machine can run more than one container!

Our `Dockerfile` is what will hold our environment configuration options for our container. This file is especially important because it is the build instructions when we build our docker image.

Wait, image? Container? What's the difference? I'm glad you asked. A Docker **container** is an instance of an image, whereas an **image** is a set of layers that holds our changes. As someone on StackOverflow mentioned, the image is the recipe and the container is the cake. If you want to see all your images, simply run `docker images`. If you want to see all your containers, simply run `docker ps -a`.

Onto the `Dockerfile`.

## References

1. https://opensource.com/resources/what-docker
2. https://docs.docker.com/install/linux/docker-ce/ubuntu/#install-using-the-repository
