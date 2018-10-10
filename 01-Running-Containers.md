# Running and Building Containers Interatively

In this section, we're going to learn how to:

1. Start Containers
2. Run containers interactively
3. Save container images
4. Using saved images

## 1. Starting Containers

To get started, we're going to start with a hello world example.

    docker run alpine echo Hello

If docker was setup properly, you should see an output that looks something like this:

    Unable to find image 'alpine:latest' locally
    latest: Pulling from library/alpine
    4fe2ade4980c: Pull complete
    Digest: sha256:621c2f39f8133acb8e64023a94dbdf0d5ca81896102b9e57c0dc184cadaf5528
    Status: Downloaded newer image for alpine:latest
    Hello

When you ran this command, you told docker that you want to **run** a command on the **alpine** Linux container, and the command you want to run is "echo Hello".

Here's what happened:

1. Docker realized you didn't have a copy of alpine Linux locally on your machine, so it downloaded a copy (Alpine Linux is only 4 MB!).
2. After it downloaded Alpine, it showed you a sha256 hash of the entire file system.  You can use this to verify the image is what it's supposed to be.  If *one* byte is changes, the entire hash changes.
3. It started the container
4. It ran "echo Hello" command
5. It stopped the container

Try it again: 

    docker run alpine echo Hello

This time you should only see "Hello" because Docker didn't have to download Alpine Linux.  It was already on your machine.

## 2. Running Containers Interactively

Now that we have a copy of Alpine Linux, let's play around at the command prompt.

    docker run -it alpine /bin/sh

You should now see a command prompt:

    / #

Let's create a file:

    echo Hello > hello.txt

Let's prove that we created the file:

    ls

You should see something like:

    bin        etc        home       media      proc       run        srv        tmp        var
    dev        hello.txt  lib        mnt        root       sbin       sys        usr

And we can look at the contents of that file:

    cat hello.txt

When we're done, we can exit our container:

    exit

This should exit the container and bring us back to our host machine, our laptops.

OOPS!!  You just lost your changes.  That's because containers forget everything you did as soon as you exit.  We say containers are *ephemeral* because the state of the container doesn't persist by default.

This is actually one of the key *features* of Docker.

I'll show you:

    docker run alpine cat hello.txt

You should see this error:

    cat: can't open 'hello.txt': No such file or directory

## Saving Container Images

What if we wanted to make our own container image with files on it and software already installed?

Well, there are two ways:

1. You can create a Dockerfile and build and image (We'll talk about that later)
2. You can do it interactively and "commit" your changes to a new container image. (We'll do this now)

Before we begin, open a second terminal (command-line) window.

In the primary window, we're going to go to the shell:

    docker run -it alpine /bin/sh

Let's create a file:

    echo Hello > hello.txt

Let's install curl so we can fetch a webpage:

    apk add curl

You should see something like:

    (1/5) Installing ca-certificates (20171114-r3)
    (2/5) Installing nghttp2-libs (1.32.0-r0)
    (3/5) Installing libssh2 (1.8.0-r3)
    (4/5) Installing libcurl (7.61.1-r0)
    (5/5) Installing curl (7.61.1-r0)
    Executing busybox-1.28.4-r1.trigger
    Executing ca-certificates-20171114-r3.trigger
    OK: 15 MiB in 23 packages

Now, switch over to the second terminal window you created so we can save the changes.

First, we're going to need to get your running container's id.  Type in:

    docker ps

You should see something like looks like this:

    CONTAINER ID        IMAGE               COMMAND             CREATED             STATUS              PORTS               NAMES
    215e92b8cea9        alpine              "/bin/sh"           3 minutes ago       Up 3 minutes                            happy_villani

You're going to save it to a new image called "alpine-hello" using the **docker commit** command:

    docker commit 215e92b8cea9 alpine-hello

If it works, it will show a hash that looks something like this:

    sha256:6ce13bba6eac548921af05ff05e04672ff4012baa48af1714b1ad3590a94cc3d

## 4. Using Your Saved Image

To prove your changes were saved, let's do a couple of tests.  First, let's make sure the hello.txt file was saved:

    docker run alpine-hello cat hello.txt

You should see "Hello"

You can also test if curl is installed:

    docker run alpine-hello curl http://www.google.com

It will show something like so:

    % Total    % Received % Xferd  Average Speed   Time    Time     Time  Current
                                    Dload  Upload   Total   Spent    Left  Speed
    0     0    0     0    0     0      0      0 --:--:--  0:00:04 --:--:--     0curl: (6) Could not resolve host: www.google.com

Remember, the original alpine image didn't have curl. If you try running curl on it:

    docker run alpine curl http://www.google.com

You'll get something like so:

    docker: Error response from daemon: OCI runtime create failed: container_linux.go:348: starting container process caused "exec: \"curl\": executable file not found in $PATH": unknown.