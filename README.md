# DockerImage
Dockerization of shiny app

Below a snapshot from the corresponding blog for this docker image. (https://schirrmeister.wordpress.com/2018/08/10/my-first-shiny-docker-image/)

(this guide focuses on mac os)
## Get started
-Install docker: https://store.docker.com/editions/community/docker-ce-desktop-mac

-Open docker (click on icon)

-Open terminal and check:

`docker info #basic system information  (how many containers, images, running etc.)`

Before you build your own container with an app, have a look at the container provided by docker

-Log into your Docker account and run the hello-world container

`docker run hello-world #runs hello-world container example from docker`

because the hello-world image above is not found locally, it will be pulled from the Docker Hub. If you see the text above it worked.

-To view your docker images:

`docker images #displays the images on your machine`

### Create a local docker container for your app

-Create a directory and go there:

`mkdir DockerRep # created new directory`
`cd DockerRep # change directory`

-Create your app and a run file:

app.R (I have created that app in another blog)

app_run.R (see content below):

```R
require(shiny) #loads shiny package
shiny::runApp("app.R", launch.browser = FALSE, port = 8080, host = "0.0.0.0") #runs shiny app in port 8080 localhost
```

-Create a Dockerfile (I am using vi commandline-editor. Feel free to use anything you like)

Dockerfile (below):

```bash
#build an image on top of the base image for r version 3.5.1 from rocker (https://hub.docker.com/r/rocker/r-ver/~/dockerfile/)
FROM rocker/r-ver:3.5.1 
#install necessary libraries
RUN R -e "install.packages(c('ggplot2','shiny'))"

#copy the current folder into the path of the app
COPY . /usr/local/src/app
#set working directory to the app
WORKDIR /usr/local/src/app

#set the unix commands to run the app
CMD ["Rscript","app_run.R"]
```

So in my ~/path/DockerRep/ I have three files: 1) app.R; 2) app_run.R; 3) Dockerfile.

Finally run:

```bash
#build an image named "mydockerapp"(needs to be lower case)
docker build -t mydockerapp .

#run the image "mydockerapp" in the container "DOCKERapp" in port 8080 
docker run --name DOCKERapp -p 8080:8080 mydockerapp
```

calling https:/localhost:8080 you can now run your containerized shiny app in the webbrowser. Additionally you'll be able to ship your container to a server, another mashine etc. The R enivironment in your container will remain the same and your app will be running smoothely, even if you happen to update your R version.

### Few further notes:

If you want to view which containers are running and have run:

```bash
docker ps #running
docker ps -a #have run
```

Be aware that everytime you use docker run image-name you are creating a new container. Instead you could run docker run -rm image-name, which will ensure the container is removed afterwards.

If you would like to delete containers:

`docker rm -f containerName #remove a container`

### TOP 5 docker commands

Below is a list of docker commands I found usefull. Vote for your favorite command in the comments section.

```bash
docker run -t -i ubuntu /bin/bash #running an ubuntu machine

docker ps #are there still container runnin

docker images #display docker images

docker build #builds docker images

docker rm -f containerName #delete containers you no longer need
```

### Helpful links

https://www.docker.com/

https://docs.docker.com/get-started/#docker-concepts (detailed instructions on where to start)

https://docs.docker.com/docker-for-mac/ (docker for mac - get started)

https://ropenscilabs.github.io/r-docker-tutorial/ (some instructions on how to run Rstudio in a container and generally on R and docker)
