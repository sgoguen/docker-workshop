# Mapping Volumes on Docker for Windows

I've found that when working with Docker for Windows, mapping local volumes to containers can
be a little complicated:

1. Relative paths don't seem to work.
2. I've found that you have to use full paths, and it only works when you allow Docker for Windows
   permission to the local C: drive (or whatever drive you're using)

docker run -it -v C:\Projects\github.com\sgoguen\docker-workshop\03-node:/myCode node ls /myCode

## Powershell

docker run -it -v $pwd\03-node:/myCode node ls /myCode

## Windows Command-Line

docker run -it -v %cd%\03-node:/myCode node ls /myCode

## Unix Systems