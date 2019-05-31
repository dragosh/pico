# The Pico Project

Object Detection and Analysis Made easy using Raspberry Pi, Apache Kafka, AWS Rekognition & Docker

![alt text](https://github.com/collabnix/pico/blob/master/thepicoproject1.png)



## What is Pico Project all about?

Imagine you are able to capture live video streams, identify objects using deep learning, and then trigger actions or notifications based on the identified objects - all using Docker containers. With Pico, you will be able to setup and run a live video capture, analysis, and alerting solution prototype.

A camera surveils a particular area, streaming video over the network to a video capture client. The client samples video frames and sends them over to AWS, where they are analyzed and stored along with metadata. If certain objects are detected in the analyzed video frames, SMS alerts are sent out. Once a person receives an SMS alert, they will likely want to know what caused it. For that, sampled video frames can be monitored with low latency using a web-based user interface.

The Pico framework uses Kafka cluster to acquire data in real-time. Kafka is a message-based distributed publish-subscribe system, which has the advantages of high throughput and perfect fault-tolerant mechanism. The type of data source is the video that generated by the cameras attached to Raspberry Pi. 





## What Technologies does Pico uses?

![alt text](https://github.com/collabnix/pico/blob/master/images/image-9.png)

- [Apache Kafka](http://collabnix.com/implementing-apache-kafka-on-docker-swarm-running-on-aws-platform-in-5-minutes/)
- Docker
- Docker Swarm
- AWS Rekognition Service

<TBD>

## Getting Started

[ Turn Your Raspberry Pi into CCTV Camera](http://collabnix.com/turn-your-raspberry-pi-into-low-cost-cctv-surveillance-camerawith-night-vision-in-5-minutes-using-docker/) 

[How to Deploy Apache Kafka on AWS Platform using Docker Swarm Mode?](http://collabnix.com/implementing-apache-kafka-on-docker-swarm-running-on-aws-platform-in-5-minutes/)

## To-do Lists

- Buy Raspberry Pi (Tested with Rpi 3) 
- Buy Heat Sink(Optional)
- Buy Pi Camera Module 
- Buy Pi camera Tripod Stand(Optional)
- Raspberry Pi 3 Model B 4-layer Dog Bone Stack Clear Case Box Enclosure 
- AWS Rekognition Access (Free tier) 

As an alternative, this set of scripts can be modified to watch any directory that contains images. For example, if you collect still images from another camera and save them to disk, you can alter the image path to run facial recognition against any new photo that is created.


## Installing Raspbian OS

Setup a Raspberry Pi with Raspbian Jessie <br />
https://www.raspberrypi.org/downloads/raspbian/ <br />


### Enable SSH 

```
service ssh start
```

### Enable SSH on boot-time

```
echo "service ssh start" >> /etc/rc.local
```

### Installing Docker

```
curl -sSL https://get.docker.com/ | sh
```

### Installing Docker Compose

```
apt install python-pip
pip install docker-compose
```

### Start Docker

```
root@node2:~# systemctl start docker
root@node2:~# docker version
Client:
 Version:           18.09.0
 API version:       1.39
 Go version:        go1.10.4
 Git commit:        4d60db4
 Built:             Wed Nov  7 00:57:21 2018
 OS/Arch:           linux/arm
 Experimental:      false

Server: Docker Engine - Community
 Engine:
  Version:          18.09.0
  API version:      1.39 (minimum version 1.12)
  Go version:       go1.10.4
  Git commit:       4d60db4
  Built:            Wed Nov  7 00:17:57 2018
  OS/Arch:          linux/arm
  Experimental:     false
root@node2:~# clear
root@node2:~# docker version
Client:
 Version:           18.09.0
 API version:       1.39
 Go version:        go1.10.4
 Git commit:        4d60db4
 Built:             Wed Nov  7 00:57:21 2018
 OS/Arch:           linux/arm
 Experimental:      false

Server: Docker Engine - Community
 Engine:
  Version:          18.09.0
  API version:      1.39 (minimum version 1.12)
  Go version:       go1.10.4
  Git commit:       4d60db4
  Built:            Wed Nov  7 00:17:57 2018
  OS/Arch:          linux/arm
  Experimental:     false
root@node2:~# ls
docker-cctv-raspbian              portainer-agent-stack.yml      tiny-cloud
dockerlabs                        real_time_object_detection.py  v19.03.0-beta1.zip
MobileNetSSD_deploy.prototxt.txt  rpi-motion-mmal
pi_object_detection.py            tf-opencv
```

### Clone this repo and install:<br />

```
root@node2:~# git clone https://github.com/collabnix/pico
Cloning into 'pico'...
remote: Enumerating objects: 156, done.
remote: Total 156 (delta 0), reused 0 (delta 0), pack-reused 156
Receiving objects: 100% (156/156), 27.45 KiB | 0 bytes/s, done.
Resolving deltas: 100% (69/69), done.
```

```
root@node2:~/pico/scripts# chmod +x install.sh
root@node2:~/pico/scripts# ./install.sh
```

### Getting started

First, you need to create a new collection on AWS Rekognition. Creating a 'home' collection would look like:

```
cd pico/scripts<br />
python add_collection.py -n 'home'<br />
```

Next, add your images to the pico/faces folder. The more images of a person the better results you will get for detection. I would recommend several different poses in different lighting.

```
cd pico/faces<br />
python ../scripts/add_image.py -i 'image.jpg' -c 'home' -l 'Tom'<br />
```

I found the best results by taking a photo in the same area that the camera will be placed, and by using the picam. If you want to do this, I created a small python script to take a photo with a 10 second delay and then puts it into the pico/faces folder. To use it:

```
cd pico/scripts<br />
python take_selfie.py<br />
```

Once complete, you can go back and rename the file and repeat the steps above to add your images to AWS Rekognition. Once you create a new collection, or add a new image, two reference files will be created as a future reference. These are useful if you plan on deleting images or collections in the future.

## To delete a face from your collection, use the following:

```cd pico/scripts<br />
python del_faces.py -i '000-000-000-000' -c 'home'<br />
```

If you need to find the image id or a collection name, reference your faces.txt and collections.txt files.

## To remove a collection:

```
cd pico/scripts<br />
python del_collections.py -c 'home'<br />
```

Note that the above will also delete all the faces you have stored in AWS. 

The last script is facematch.py. If you have images updated and just want to test static photos against the faces you have stored on AWS, do the following:

```
cd pico/scripts<br />
python facematch.py -i 'tom.jpg' -c 'home'<br />
```

The results will be printed to screen, to include the percentage of similarity and confidence. 
