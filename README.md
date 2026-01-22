# docker-ci-heroku-20

A set of Docker images to emulate (pretty closely) the various heroku build stacks and ruby version combinations.

The purpose is for these to be usable on Bitbucket pipelines. Any other compatibility is a bonus and will only be supported
if support requires minimal effort.

## Inheriting this Docker image

Example Dockerfile:

```Dockerfile
# We highly suggest you pin your FROM to a specific verson of this image
FROM blakeisrael/heroku-ci-ruby-3.2.1-node
```

## Building an Image

IF YOU ARE ON A MAC M1/M2/M3/M1000 you will first need to install docker buildx:

`docker buildx install`

### THEN:

`cd` into the appropriate directory.

Build the image and tag it (replace `blakeisrael` with your docker hub username or orgname):

If you are on an Intel chip:

`docker build . -t blakeisrael/${PWD##*/}`

If you are on Apple Silicon:

`docker buildx build --platform=linux/amd64 . -t blakeisrael/${PWD##*/}`

`${PWD##*/}` will be replaced with the current directory name.

Push it to docker:

`docker push blakeisrael/${PWD##*/}`

You can then `FROM blakeisrael/some-tag-name` in your own other Dockerfile OR
you can use it as the base in your `bitbucket-pipelines.yml` with `image: blakeisrael/some-tag-name`.

## I just want to use it

These images are all built and uploaded to my docker hub: https://hub.docker.com/u/blakeisrael

They are used (or were used) in the real world every day. The most recent ruby versions will likely be maintained decently enough.
