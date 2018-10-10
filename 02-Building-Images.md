# Building Container Images

In the last section, we built a container image interactive from the terminal.

In this section, we're going to use the **docker build** command to turn a "Dockerfile" into a container image.

## Hello Ubuntu!

In the last section, we opened the terminal and ran the following commands:

    echo Hello > hello.txt
    apk add curl

In this section, we're going to create a Dockerfile that will do it for us.  Let's walk through the steps:

    # First, let's create a folder for this Dockerfile
    mkdir 02-hello-alpine

    # Go to that directory
    cd 02-hello-alpine

Create a file in our new folder and call it "Dockerfile"

    # This tells Docker that our new image is based on the alpine image
    FROM alpine

    # Let's create the hello.txt file again
    RUN echo Hello Alpine! > hello.txt

    # Let's install curl!
    RUN apk add curl

Note: Comments in docker files begin with the "#" symbol

## Let's Build It!

Now we use the **docker build** command to build our image:

    docker build -t hello-alpine2 .

You will see an output that looks something like this:

    Sending build context to Docker daemon  2.048kB
    Step 1/3 : FROM alpine
    ---> 196d12cf6ab1
    Step 2/3 : RUN echo Hello Alpine! > hello.txt
    ---> Running in a3aea4952f89
    Removing intermediate container a3aea4952f89
    ---> c6744690d7d0
    Step 3/3 : RUN apk add curl
    ---> Running in e51d2181ae0d
    fetch http://dl-cdn.alpinelinux.org/alpine/v3.8/main/x86_64/APKINDEX.tar.gz
    fetch http://dl-cdn.alpinelinux.org/alpine/v3.8/community/x86_64/APKINDEX.tar.gz
    (1/5) Installing ca-certificates (20171114-r3)
    (2/5) Installing nghttp2-libs (1.32.0-r0)
    (3/5) Installing libssh2 (1.8.0-r3)
    (4/5) Installing libcurl (7.61.1-r0)
    (5/5) Installing curl (7.61.1-r0)
    Executing busybox-1.28.4-r1.trigger
    Executing ca-certificates-20171114-r3.trigger
    OK: 6 MiB in 18 packages
    Removing intermediate container e51d2181ae0d
    ---> 287fa4941383
    Successfully built 287fa4941383
    Successfully tagged hello-alpine2:latest

Notice how the output shows each step numbered.  This is useful when you're trying to figure our why Dockerfile isn't building.

Let's test it!

    > docker run hello-alpine2 cat hello.txt
    Hello Alpine!

Let's compare it to the first image we built.  It was called "alpine-hello"

    > docker run alpine-hello cat hello.txt
    Hello

# Deleting Images

Building an image from a Dockerfile is the preferred way to build an image because it's predictable, repeatable and sharable.

Now that we have an image built from a Dockerfile, let's clean up our images on our machine.

First, enter the command to show all the images on your machine:

    > docker images
    REPOSITORY                 TAG                 IMAGE ID            CREATED             SIZE
    hello-alpine2              latest              287fa4941383        5 minutes ago       7.21MB
    alpine-hello               latest              6ce13bba6eac        2 hours ago         11MB
    alpine                     latest              196d12cf6ab1        4 weeks ago         4.41MB

You can see here I have a three images.  I want to delete the alpine-hello image, so I'll use the **docker image rm** command.

    > docker image rm alpine-hello
    Error response from daemon: conflict: unable to remove repository reference "alpine-hello" (must force) - container 01cb3f9b9db6 is using its referenced image 6ce13bba6eac

Here I got an error.  You may get an error, and you may not.  In my case, I got an error because a container (think running instance) was using it.

I can remove the container like so:

    docker container rm 01cb3f9b9db6

Or I can just tell docker to force delete the image (which will also remove the container)

    docker image rm -f alpine-hello

While we could always force remove the image, you should be careful.  Forcing the removal of an image can accidentally remove something you don't want to remove.
