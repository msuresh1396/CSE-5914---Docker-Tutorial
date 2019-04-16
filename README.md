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

### Creating the `Dockerfile`

1. Start by creating a new, empty directory anywhere on your local machine and change into it:

    ```
    mkdir docker-test && cd docker-test
    ```
    
2. Within your new directory, create a file called `Dockerfile` and copy-paste the following content into it:

    ```
    # Use an official Python runtime as a parent image
    FROM python:2.7-slim

    # Set the working directory to /app
    WORKDIR /app

    # Copy the current directory contents into the container at /app
    COPY . /app

    # Install any needed packages specified in requirements.txt
    RUN pip install --trusted-host pypi.python.org -r requirements.txt

    # Make port 80 available to the world outside this container
    EXPOSE 80

    # Define environment variable
    ENV NAME world

    # Run app.py when the container launches
    CMD ["python", "app.py"]
    ```
    
3. Now, we need to create two more files in the same folder, namely `requirements.txt` and `app.py`:

    **requirements.txt**:
    ```
    Flask
    ```
    
    **app.py**:
    ```
    import os
    import socket
    from flask import Flask

    app = Flask(__name__)

    @app.route("/")
    def hello():
        html = "<h3>Hello, {name}!</h3>" \
               "<b>Hostname:</b> {hostname}<br/>"
        return html.format(name=os.getenv("NAME", "world"), hostname=socket.gethostname())

    if __name__ == "__main__":
        app.run(host='0.0.0.0', port=80)
    ```
    
That's it! Now we are ready to build the app.

### Building the App

1. In the directory with the `Dockerfile` file, `requirements.txt` file, and `app.py` file, run the build command (we name it using the `tag` command):

    ```
    docker build --tag=testinghello .
    ```
    
    Remember how I talked about images vs containers?
    
    Well, if you run `docker image ls`, you will see the image we just created.
    
### Run the App

1. Run the app, mapping your machine's port 8080 to the container's published port 80:

    ```
    docker run -p 8080:80 testinghello
    ```
    
    Now, if you go to `http://localhost:8080`, you should see the content served up on a webpage!

    Hit `CTRL+C` in your terminal to quit.
    
2. Run the app in the background in a detached mode:

    ```
    docker run -d -p 8080:80 testinghello
    ```
    
3. You can view the container by running:

    ```
    docker container ls
    
    Output:
    CONTAINER ID        IMAGE               COMMAND             CREATED
    1fa4ab2cf395        testinghello       "python app.py"     28 seconds ago
    ```
    
4. You can stop the container by running:

    ```
    docker container stop 1fa4ab2cf395
    ```
    
## Step 2.1: Getting Other People's Docker Images

Let's say a friend told you about Docker and they told you to pull their awesome nginx web server image from Dockerhub.

What does that even mean? Well, the Docker community has a hub sort of like Github. People upload images to Dockerhub so that their friends or other people can pull them and easily run them in a container. It saves you from having to create a Docker image from scratch when somebody has already gone through the pain of doing it!

So, how do we get started?

1. First, we'll test this with the [nginx web server](https://nginx.org/en/):

    ```
    docker pull nginx
    ```
    
    If you run `docker images`, you will see that `nginx` is also there.
    
2. Let's run our nginx Docker container:

    ```
    docker run --name docker-nginx -p 8080:80 -d nginx
    ```
    
    * We create a new container with `run`.
    * And name it `docker-nginx` using `--name`.
    * We map local port 8080 to the container port 80 with `-p`.
    * We run the container in detached mode using `-d`.
    * And `nginx` is the name of the image we pulled from Dockerhub (note: if the image wasn't downloaded, Docker will do this automatically).

3. Say you want to access the nginx container to do a bit of configuration. That is possible by running the following command:

    ```
    docker exec -it <container-id> bash
    ```
    
    where `<container-id>` is the ID of the running container.
    
    You should now be at the commandline within your container. You can now make configuration changes via the shell.
    
    Once you are done, simply run `exit`.
    
4. Just like before, you can navigate to `http://localhost:8080` to see that the nginx web server is running.

## Step 3: Docker Compose

The last part of this Docker Quickstart Tutorial will cover Docker Compose.

### What is Docker Compose?

Docker Compose is "a tool for defining and running multi-container Docker applications. With Compose, you use a YAML file to configure your application’s services. Then, with a single command, you create and start all the services from your configuration."

### Installing Docker Compose

1. Download the current stable release of Docker Compose:

    ```
    sudo curl -L "https://github.com/docker/compose/releases/download/1.24.0/docker-compose-$(uname -s)-$(uname -m)" -o /usr/local/bin/docker-compose
    ```
    
2. Apply executable permissions to the binary:

    ```
    sudo chmod +x /usr/local/bin/docker-compose
    ```
    
3. Test the installation:

    ```
    docker-compose --version
    ```
    
### Creating Wordpress and MySQL Containers

Using Docker Compose, we will cover how to setup a Docker container for Wordpress and one for MySQL. As a side note, you can also create a `Dockerfile` to build the Docker image. That is only necessary if you want to configure your environment a certain way.

1. Create a `docker-compose.yml` file with the following content:

    ```
    version: '3'

    services:
       db:
         image: mysql:5.7
         volumes:
           - db_data:/var/lib/mysql
         restart: always
         environment:
           MYSQL_ROOT_PASSWORD: somewordpress
           MYSQL_DATABASE: wordpress
           MYSQL_USER: wordpress
           MYSQL_PASSWORD: wordpress

       wordpress:
         depends_on:
           - db
         image: wordpress:latest
         ports:
           - "8080:80"
         restart: always
         environment:
           WORDPRESS_DB_HOST: db:3306
           WORDPRESS_DB_USER: wordpress
           WORDPRESS_DB_PASSWORD: wordpress
    volumes:
        db_data:
    ```
    
2. After saving the file, run the following command from the same directory:

    ```
    docker-compose up -d
    ```
    
    This will build and run the `db` and `wordpress` containers in the detached mode.
    
3. Navigate to `http://localhost:8080/wordpress` to see the new installation.
4. Stop and remove the containers by running `docker-compose down`.

### `Dockerfile` vs `docker-compose.yml`

What is the difference between these two files? The `Dockerfile` configures and sets up the environment of the Docker container. The `docker-compose.yml` file is what defines and configures the services running on the Docker container.

## Step 4: Useful Commands

This step isn't really a step but more of a reference. Here we list a bunch of useful Docker commands:

* `docker start <image-name>`: start the docker image with `<image-name>`
* `docker stop <image-name>`: stop the docker image with `<image-name>`
* `docker rm <image-name>`: delete a docker image with `<image-name>`
* `docker ps`: list running docker containers (use `-a` flag to list all)
* `docker images`: list docker images
* `docker pull <image-name>`: download docker image from Dockerhub

## Step 5: Other Docker Terms

Here are some useful Docker terms found in this tutorial as well as around the Docker community!

* 'Docker for Windows/Linux/Mac': Docker can be run on multiple different operating systems
* 'Docker Engine': Use to build Docker images and creating the Docker containers
* 'Docker Hub': Your one stop shop for all Docker images
* 'Docker Compose': This is used when you want to define applications that are using multiple different containers
* 'Official Images': These are images that are the official image of whatever product it is, this helps avoid scams and malicious images

## References

1. [https://opensource.com/resources/what-docker](https://opensource.com/resources/what-docker)
2. [https://docs.docker.com/install/linux/docker-ce/ubuntu/#install-using-the-repository](https://docs.docker.com/install/linux/docker-ce/ubuntu/#install-using-the-repository)
3. [https://docs.docker.com](https://docs.docker.com)
4. [https://www.linode.com/docs/applications/containers/how-to-install-docker-and-pull-images-for-container-deployment/](https://www.linode.com/docs/applications/containers/how-to-install-docker-and-pull-images-for-container-deployment/)
5. [https://www.techrepublic.com/article/how-to-run-nginx-as-a-docker-container/](https://www.techrepublic.com/article/how-to-run-nginx-as-a-docker-container/)
6. [https://docs.docker.com/compose/overview/](https://docs.docker.com/compose/overview/)
7. [https://www.linode.com/docs/applications/containers/how-to-use-docker-compose/](https://www.linode.com/docs/applications/containers/how-to-use-docker-compose/)
8. [https://www.tutorialspoint.com/docker/](https://www.tutorialspoint.com/docker/)
