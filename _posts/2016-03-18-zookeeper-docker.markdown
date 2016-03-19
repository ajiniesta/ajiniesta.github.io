---
layout: post
title:  "Dockerized Zookeeper in Alpine Linux"
date:   2016-03-18 18:05:20
categories: docker zookeeper
comments: true
---
![Docker Alpine Zookeeper]({{site.url}}/images/daz.png)

I've been playing around a while with docker. And I started a personal project, to interact with Zookeeper from a desktop application. So I finally decide use one of this images and start programing using a dockerized image.

Then, soon I realized the image of a container with Ubuntu, to run just one service, like zookeeper, was pretty nice. So I decide, to go for Alpine Linux, a lightweight distribution.

To start deepest with docker, I recommend the docker [documentation][docker-tutorials] and some [self-paced-training][docker-training] in the official page. Here I'm going to show how I build, tag, control and push this images.

First, I started creating an [docker image with a oracle jre][docker-jre] to be the base of the [zookeeper docker image][docker-zk].

The `CMD` of the Dockerfile is the start of the server in foreground, so we just need to run the image to create a container running with Zookeeper. And all the images are now pushed to the docker hub, so you just need to enter this command to execute, and automatically will be pull the image and execute the container.

{%highlight bash%}
docker run -i -t -p 2181:2181 ajiniesta/alpinezoo
{%endhighlight%}

To build this images based on the Dockerfile, first we are going to put this Dockerfile in a folder. And build the image, will be exactly the same for the java and zookeeper images, so I'm just going to do it for the zookeeper one.

{%highlight bash%}
docker build -t ajiniesta/alpinezoo
{%endhighlight%}

The `-t` option in the build, will name a tag for the container, allowing a version name optional. Remember that is important the `ajiniesta/alpinezoo` the `ajiniesta` is the username, in this case mine. If you don't have any, go to [docker hub][docker-hub] and create an account. Because later this id will be mandatory to push the image to the docker hub. By default the tag version will be `latest`.

Now with the command `docker images` we are showing all the images built and pulled.

{%highlight bash%}
$ docker images
REPOSITORY             TAG                 IMAGE ID            CREATED             SIZE
ajiniesta/alpinezoo    latest              6300df4cb8e4        5 hours ago         249.2 MB
ajiniesta/alpinejava   jre8u74             c9b46be4db5d        5 hours ago         215.3 MB
{%endhighlight%}

Now we need to `docker login` and input our credentials if we want to push our image. Then we can push the image(`docker push ajiniesta/alpinezoo`) and our docker image will be available for the community.

Also if we want to stop our docker container, we can `Ctrl+C` or stop the container via console in another console. We execute `docker ps`, locate our running container, and execute `docker stop 'id-of-the-container'`

[docker-hub]: https://hub.docker.com
[docker-tutorials]: https://docs.docker.com/
[docker-training]: https://training.docker.com/self-paced-training
[docker-jre]: https://github.com/ajiniesta/alpine-dockers/blob/master/alpinejava/Dockerfile
[docker-zk]:  https://github.com/ajiniesta/alpine-dockers/blob/master/alpinezoo/Dockerfile
