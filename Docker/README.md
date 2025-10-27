# Introduction

## What is Docker?

Wikipedia defines [Docker](https://www.docker.com/) as:

> an open-source project that automates the deployment of software applications inside containers by providing an additional layer of abstraction and automation of OS-level virtualization on Linux.

Wow! That's a mouthful. In simpler words, **Docker** is a tool that allows developers, sys-admins, etc. to easily deploy their applications in a sandbox (called **containers**) to run on the host operating system (i.e., Linux).

The key benefit of Docker is that it allows users to **package an application with all of its dependencies into a standardized unit for software development**.

Unlike virtual machines, containers do not have high overhead and hence enable more efficient usage of the underlying system and resources.

---

## What are Containers?

The industry standard today is to use **Virtual Machines (VMs)** to run software applications.  
VMs run applications inside a **guest Operating System**, which runs on virtual hardware powered by the server’s host OS.

VMs are great at providing full process isolation for applications:  
There are very few ways a problem in the host operating system can affect the software running in the guest operating system, and vice-versa.

But this isolation comes at a great cost — the computational overhead spent virtualizing hardware for a guest OS to use is substantial.

**Containers take a different approach:**  
By leveraging the low-level mechanics of the host operating system, containers provide most of the isolation of virtual machines at a fraction of the computing power.

---

## Why Use Containers?

Containers offer a **logical packaging mechanism** in which applications can be abstracted from the environment in which they actually run.

This decoupling allows container-based applications to be **deployed easily and consistently**, regardless of whether the target environment is a private data center, the public cloud, or even a developer’s personal laptop.

This gives developers the ability to create **predictable environments** that are isolated from the rest of the applications and can be run anywhere.

From an operations standpoint, apart from portability, containers also give **more granular control over resources**, giving your infrastructure improved efficiency, which can result in better utilization of your compute resources.

---

## Docker Interest Over Time

> _Google Trends for Docker_

Due to these benefits, containers (& Docker) have seen **widespread adoption**.  
Companies like **Google, Facebook, Netflix, and Salesforce** leverage containers to make large engineering teams more productive and to improve utilization of compute resources.

In fact, **Google credited containers for eliminating the need for an entire data center.**

---

## What Will This Tutorial Teach Me?

This tutorial aims to be the **one-stop shop** for getting your hands dirty with Docker.  
Apart from demystifying the Docker landscape, it will give you **hands-on experience** with building and deploying your own web apps on the Cloud.

We'll be using **Amazon Web Services (AWS)** to deploy:

- A static website
- Two dynamic web apps on [EC2](https://aws.amazon.com/ec2/) using **[Elastic Beanstalk](https://aws.amazon.com/elasticbeanstalk/)** and **[Elastic Container Service (ECS)](https://aws.amazon.com/ecs/)**

Even if you have no prior experience with deployments, this tutorial should be all you need to get started.

---

# Getting Started

This document contains a series of several sections, each of which explains a particular aspect of Docker.  
In each section, we will be typing commands (or writing code).  
All the code used in the tutorial is available in the [GitHub repo](https://github.com/prakhar1989/docker-curriculum).

> **Note:** This tutorial uses version **18.05.0-ce** of Docker.  
> If you find any part of the tutorial incompatible with a future version, please raise an issue. Thanks!

---

## Prerequisites

There are no specific skills needed for this tutorial beyond a basic comfort with the command line and using a text editor.  
This tutorial uses `git clone` to clone the repository locally.

If you don't have Git installed on your system, either install it or manually download the ZIP files from GitHub.

Prior experience in developing web applications will be helpful but is **not required**.  
As we proceed further along the tutorial, we'll make use of a few cloud services.

If you're interested in following along, please create an account on each of these websites:

- [Amazon Web Services (AWS)](https://aws.amazon.com)
- [Docker Hub](https://hub.docker.com)

---

## Setting up your computer

Getting all the tooling setup on your computer can be a daunting task,  
but thankfully as Docker has become stable, getting Docker up and running on your favorite OS has become very easy.

Until a few releases ago, running Docker on OSX and Windows was quite a hassle.  
Lately however, Docker has invested significantly into improving the onboarding experience for its users on these OSes,  
thus running Docker now is a cakewalk.

The [Getting Started guide on Docker](https://docs.docker.com/get-started/)  
has detailed instructions for setting up Docker on **Mac**, **Linux**, and **Windows**.

---

## Testing your installation

Once you are done installing Docker, test your Docker installation by running the following command:

````bash
$ docker run hello-world


# Hello World

## Playing with Busybox

Now that we have everything setup, it's time to get our hands dirty.
In this section, we are going to run a **Busybox** container on our system and get a taste of the `docker run` command.

---

### Pull the Busybox Image

To get started, let's run the following in our terminal:

```bash
$ docker pull busybox
````

> **Note:** Depending on how you've installed Docker on your system, you might see a permission denied error after running the above command.
>
> - If you're on a **Mac**, make sure the Docker engine is running.
> - If you're on **Linux**, prefix your Docker commands with `sudo`.
>   Alternatively, you can create a Docker group to get rid of this issue.

The `pull` command fetches the **busybox** image from the Docker registry and saves it to your system.
You can use the `docker images` command to see a list of all images on your system.

```bash
$ docker images
REPOSITORY          TAG       IMAGE ID       CREATED         VIRTUAL SIZE
busybox             latest    c51f86c28340   4 weeks ago     1.109 MB
```

---

## Docker Run

Great! Let's now run a Docker container based on this image.
To do that we are going to use the almighty `docker run` command.

```bash
$ docker run busybox
$
```

Wait, nothing happened! Is that a bug?
Well, no. Behind the scenes, a lot of stuff happened.

When you call `run`, the Docker client finds the image (busybox in this case), loads up the container, and then runs a command in that container.
When we run `docker run busybox`, we didn’t provide a command, so the container booted up, ran an empty command, and then exited.

Let’s try something more exciting:

```bash
$ docker run busybox echo "hello from busybox"
hello from busybox
```

Nice — finally we see some output.
In this case, the Docker client ran the `echo` command inside our Busybox container and then exited it.

If you’ve noticed, all of that happened **really fast** — much faster than booting up a VM!
Now you know why they say **containers are fast**.

---

## Listing Containers

Let’s see what containers are currently running using the `docker ps` command.

```bash
$ docker ps
CONTAINER ID   IMAGE     COMMAND   CREATED   STATUS   PORTS   NAMES
```

Since no containers are running, we see a blank line.
Let’s try a more useful variant: `docker ps -a`

```bash
$ docker ps -a
CONTAINER ID   IMAGE        COMMAND     CREATED         STATUS                      PORTS   NAMES
305297d7a235   busybox      "uptime"    11 minutes ago  Exited (0) 11 minutes ago          distracted_goldstine
ff0a5c3750b9   busybox      "sh"        12 minutes ago  Exited (0) 12 minutes ago          elated_ramanujan
14e5bd11d164   hello-world  "/hello"    2 minutes ago   Exited (0) 2 minutes ago           thirsty_euclid
```

What we see above is a list of all containers that we ran.
Notice that the **STATUS** column shows that these containers exited a few minutes ago.

---

## Running Interactive Containers

You’re probably wondering if there’s a way to run more than just one command in a container. Let’s try that now:

```bash
$ docker run -it busybox sh
/ # ls
bin   dev   etc   home  proc  root  sys   tmp   usr   var
/ # uptime
05:45:21 up  5:58,  0 users,  load average: 0.00, 0.01, 0.04
```

Running the `run` command with the `-it` flags attaches us to an **interactive TTY** inside the container.
Now we can run as many commands as we want inside the container.

Take some time to run your favorite commands!

---

### ⚠️ Danger Zone

If you’re feeling adventurous, try this (but be careful!):

```bash
/ # rm -rf bin
```

Make **sure** you run this inside the **container**, not your laptop/desktop.
Doing this will make commands like `ls` or `uptime` stop working.

Once everything breaks, you can exit the container (type `exit` and press Enter),
then start a new one again with:

```bash
$ docker run -it busybox sh
```

Since Docker creates a **new container** every time, everything should work again.

---

## Cleaning Up Containers

Throughout this tutorial, you’ll run `docker run` multiple times, and leaving stray containers will eat up disk space.
As a rule of thumb, clean up containers once you’re done with them.

To delete specific containers, use `docker rm` with their IDs:

```bash
$ docker rm 305297d7a235 ff0a5c3750b9
305297d7a235
ff0a5c3750b9
```

If you have many exited containers, you can remove them all in one go:

```bash
$ docker rm $(docker ps -a -q -f status=exited)
```

- The `-q` flag only returns numeric IDs
- The `-f` flag filters the output based on the condition (in this case, status=exited)

You can also use the `--rm` flag when running containers, so they delete automatically when exited:

```bash
$ docker run --rm busybox echo "temporary container"
```

In later Docker versions, you can use:

```bash
$ docker container prune
WARNING! This will remove all stopped containers.
Are you sure you want to continue? [y/N] y
Deleted Containers:
4a7f7eebae0f6...
f98f9c2aa1ea...

Total reclaimed space: 212 B
```

Lastly, you can also delete images that you no longer need using:

```bash
$ docker rmi <image_id>
```

---

## Terminology

In the last section, we used a lot of Docker-specific jargon.
Let’s clarify some of these key terms used throughout the Docker ecosystem.

### **Images**

The **blueprints** of our application which form the basis of containers.
In the demo above, we used the `docker pull` command to download the Busybox image.

### **Containers**

Created from Docker images and run the actual application.
We create a container using `docker run`.
A list of running containers can be seen using the `docker ps` command.

### **Docker Daemon**

The background service running on the host that manages building, running, and distributing Docker containers.
The daemon is the process that runs in the OS and listens for commands from clients.

### **Docker Client**

The **command-line tool** that allows the user to interact with the Docker daemon.
Other clients like **Kitematic** provide a GUI for interacting with Docker.

### **Docker Hub**

A **registry of Docker images** — think of it as a directory of all available Docker images.
Users can host their own private registries as well for custom image distribution.

---

> ✅ **Up Next:** Building Our First Image →

---

# Webapps with Docker

Great! So we have now looked at `docker run`, played with a Docker container and also got a hang of some terminology. Armed with all this knowledge, we are now ready to get to the real-stuff, i.e. deploying web applications with Docker!

---

## Static Sites

Let's start by taking baby-steps. The first thing we're going to look at is how we can run a dead-simple static website. We're going to pull a Docker image from Docker Hub, run the container and see how easy it is to run a webserver.

Let's begin. The image that we are going to use is a single-page website that I've already created for the purpose of this demo and hosted on the registry - `prakhar1989/static-site`.  
We can download and run the image directly in one go using `docker run`.  
As noted above, the `--rm` flag automatically removes the container when it exits and the `-it` flag specifies an interactive terminal which makes it easier to kill the container with `Ctrl+C` (on Windows).

```bash
$ docker run --rm -it prakhar1989/static-site
```

Since the image doesn't exist locally, the client will first fetch the image from the registry and then run the image.
If all goes well, you should see a `Nginx is running...` message in your terminal.

Now that the server is running, how to see the website? What port is it running on?
Hit `Ctrl+C` to stop the container.

Well, in this case, the client is not exposing any ports so we need to re-run the `docker run` command to publish ports.
While we're at it, we should also find a way so that our terminal is not attached to the running container.
This is called **detached mode**.

```bash
$ docker run -d -P --name static-site prakhar1989/static-site
```

Example output:

```
e61d12292d69556eabe2a44c16cbd54486b2527e2ce4f95438e504afb7b02810
```

In the above command:

- `-d` will detach our terminal,
- `-P` will publish all exposed ports to random ports, and
- `--name` corresponds to a name we want to give.

Now we can see the ports by running:

```bash
$ docker port static-site
```

Output:

```
80/tcp -> 0.0.0.0:32769
443/tcp -> 0.0.0.0:32768
```

You can open [http://localhost:32769](http://localhost:32769) in your browser.

> **Note:** If you're using `docker-toolbox`, then you might need to use
> `docker-machine ip default` to get the IP.

You can also specify a custom port:

```bash
$ docker run -p 8888:80 prakhar1989/static-site
```

Output:

```
Nginx is running...
static site
```

To stop a detached container, run:

```bash
$ docker stop static-site
```

Output:

```
static-site
```

To deploy this on a real server you would just need to install Docker, and run the above Docker command.

---

## Docker Images

We've looked at images before, but in this section we'll dive deeper into what Docker images are and build our own image!

To see the list of images available locally:

```bash
$ docker images
```

Example output:

```
REPOSITORY                      TAG                 IMAGE ID            CREATED             VIRTUAL SIZE
prakhar1989/catnip              latest              c7ffb5626a50        2 hours ago         697.9 MB
prakhar1989/static-site         latest              b270625a1631        21 hours ago        133.9 MB
python                          3-onbuild           cf4002b2c383        5 days ago          688.8 MB
martin/docker-cleanup-volumes   latest              b42990daaca2        7 weeks ago         22.14 MB
ubuntu                          latest              e9ae3c220b23        7 weeks ago         187.9 MB
busybox                         latest              c51f86c28340        9 weeks ago         1.109 MB
hello-world                     latest              0a6ba66e537a        11 weeks ago        960 B
```

The `TAG` refers to a particular snapshot of the image and the `IMAGE ID` is its unique identifier.

Think of an image akin to a git repository — images can be committed with changes and have multiple versions.
If you don't provide a specific version number, Docker defaults to `latest`.

Example:

```bash
$ docker pull ubuntu:18.04
```

### Base vs Child Images

- **Base images** → images with no parent (e.g. `ubuntu`, `busybox`, `debian`)
- **Child images** → built on base images and add more functionality

Also:

- **Official images** → maintained by Docker (usually one-word names)
- **User images** → created by users (e.g. `username/image-name`)

---

## Our First Image

Let's create our own image for a simple Flask app.

Clone the repo:

```bash
$ git clone https://github.com/prakhar1989/docker-curriculum.git
$ cd docker-curriculum/flask-app
```

### Dockerfile

Create a new file called `Dockerfile` in the same directory:

```dockerfile
FROM python:3.8

# set a directory for the app
WORKDIR /usr/src/app

# copy all the files to the container
COPY . .

# install dependencies
RUN pip install --no-cache-dir -r requirements.txt

# define the port number the container should expose
EXPOSE 5000

# run the command
CMD ["python", "./app.py"]
```

Build the image:

```bash
$ docker build -t yourusername/catnip .
```

Run it:

```bash
$ docker run -p 8888:5000 yourusername/catnip
```

Output:

```
 * Running on http://0.0.0.0:5000/ (Press CTRL+C to quit)
```

Visit [http://localhost:8888](http://localhost:8888).

Congratulations 🎉 You just created your first Docker image!

---

## Docker on AWS

Let's deploy this app using AWS Elastic Beanstalk (EB).

### Docker Push

Login to Docker Hub:

```bash
$ docker login
```

Then push your image:

```bash
$ docker push yourusername/catnip
```

Now anyone can run your app:

```bash
$ docker run -p 8888:5000 yourusername/catnip
```

---

## Beanstalk

AWS Elastic Beanstalk is a PaaS service from AWS that manages scaling, monitoring, and deployment for you.
We'll use it to deploy our container.

Steps:

1. Log in to your [AWS Console](https://aws.amazon.com/console/).
2. Go to **Elastic Beanstalk** → Create New Application.
3. Choose **Web Server Environment**.
4. Under **Platform**, choose **Docker**.
5. Upload your `Dockerrun.aws.json` file.

Example `Dockerrun.aws.json`:

```json
{
  "AWSEBDockerrunVersion": "1",
  "Image": {
    "Name": "prakhar1989/catnip",
    "Update": "true"
  },
  "Ports": [
    {
      "ContainerPort": 5000,
      "HostPort": 8000
    }
  ],
  "Logging": "/var/log/nginx"
}
```

Once it's deployed, open the provided URL to see your app live.

---

## Cleanup

After you're done, terminate the EB environment to avoid extra charges.

Congratulations 🎉
You’ve deployed your first Docker application on AWS!

---
