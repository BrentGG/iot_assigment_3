# IOT Assignment 3

In this assignment I managed to cross-compile a simple C program using Docker buildx (on Windows) and run it on the Pynq-Z2.

The first step was to create a new Docker buildx builder using the following commands. This isn't strictly necessary but it is recommended so you always have the default builder to fall back on.
```
docker buildx create --name pynqbuilder
```
```
docker buildx use pynqbuilder
```

## Using DockerHub

Then it was time to build using buildx and push the images to a registry. I tried to use the Github registry for this but I couldn't get this to work so decided to use Dockerhub instead (it turns out using de Github registry is actually just as easy, the process is described in the next section). First you must login to Dockerhub, then the command goes as follows:
```
docker login
```
```
docker buildx build --platform linux/amd64,linux/arm64,linux/arm/v7 -t brentgg/iot_assignment_3:latest --push .
```
This can also be seen in the image below. I misspelled ```iot_assigment_3``` so now the Dockerhub repository is missing a 't', pay no attention to that.

![docker buildx build](buildx.PNG?raw=true)

Now the Dockerhub repository contains three versions of the same image, one for every architecture specified in the ```buildx build``` command. At this point we can move on to the Pynq where Docker has to be installed. This can be done like so:
```
sudo apt-get update
```
```
sudo apt-get install -y docker.io
```
And check the install using
```
docker -v
```
Lastly we can pull and run the image like so:
```
sudo docker pull brentgg/io_assignment_3:latest
```
```
sudo docker run brentgg/io_assignment_3
```
At which point "Hello World" will be printed to the console, without a newline... on purpose. This can be seen in the image below.

![running the image on the Pynq](on_pynq.PNG?raw=true)

## Using GitHub

To use the Github registry you must first login to it using Docker.
```
docker login ghcr.io -u brentgg -p TOKEN
```
Then building using buildx is the same except the path has ```ghcr.io/``` in front of it:
```
docker buildx build --platform linux/amd64,linux/arm64,linux/arm/v7 -t ghcr.io/brentgg/iot_assignment_3:latest --push .
```

![docker buildx build and push to ghcr instead of dockerhub](buildx_ghcr.PNG?raw=true)

Don't forget to install Docker:
```
sudo apt-get update
```
```
sudo apt-get install -y docker.io
```
And check the install using
```
docker -v
```

The process of pulling and running this image on the Pynq is once again the same but with a different path. It must be noted that I had to change the visibility of the image to public to be able to pull it on the Pynq, even though I was logged into ghcr.
```
sudo docker pull ghcr.io/brentgg/iot_assignment_3:latest
```
```
sudo docker run ghcr.io/brentgg/io_assignment_3
```

![running the ghcr image on the Pynq](on_pynq_ghcr.PNG?raw=true)
