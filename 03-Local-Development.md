# Local Development - Mapping Volumes to Local Directories

For a developer, it would be wonderful to have a development machine with 
everything you need ready to go.

In the docker ecosphere there are many container images that are ready to go.  
For example, if I didn't want want to install node on my local machine, I 
could use one of many of Docker's node images.  Let's try it out:

    # Let's start a node container in interactive mode
    docker run -it node bash

    # We'll create a simple hello.js file
    echo "console.log('Hello')" > hello.js

    # Then run it using node
    node hello.js

We did all that without installing node on our local development machine.  
This can be especially convienent if your application is particularly 
sensitive to a specific version of node.

For example, I can actually specify an older version of node like so

    > docker run node:6.14.4 node -v
    v6.14.4

Here I'm running the "node:6.14.4" image (notice the version after the colon),
and I'm telling it to run the command "node -v", which should print the version.

Running it on the default version shows v10.8.0:

    > docker run node node -v
    v10.8.0

## Mapping a Local Folder

That's all well and good, but we want to use our editors.  Is there a way we can
launch a Docker container and map our local files to a specific folder in the 
Docker container?





