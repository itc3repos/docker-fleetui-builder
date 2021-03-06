# Build fleet-ui docker image

Feature: No need to install required packages on a host in order to build fleetui image. This tool builds a 'builder' image and uses it to build [purpleworks/fleet-ui](https://github.com/purpleworks/fleet-ui.git).
 
## Quick start

Take a look at the build.sh if you need to change the default fleetctl version or fleet-ui.git repo. Then:

	./build.sh
	
  It builds two images. The first one is _fleet-ui-builder_, and then the script calls it to build _fleet-ui_, which is the image you will run as a service.
  
  You can customize the default fleet-ui repo or fleetctl version in the build.sh.

## Use ready-make docker-fleet-ui builder image
```
docker pull xuwang/docker-fleetui-builder
```

Replace the FLEETUIREPO with your fleet-ui repo or use the default [purpleworks/fleet-ui](https://github.com/purpleworks/fleet-ui.git)
```
$ docker run -t --rm \
	-v /var/run/docker.sock:/var/run/docker.sock \
	--env FLEET_VERSION=v0.11.8 \
	--env FLEETUIREPO=github.com/purpleworks/fleet-ui \
	--dns 8.8.8.8 \
	xuwang/docker-fleetui-builder
```

The output:

```
+ docker run -t --rm -v /var/run/docker.sock:/var/run/docker.sock --env FLEETUIREPO=github.com/xueshanf/fleet-ui --dns 8.8.8.8 xuwang/docker-fleetui-builder
FLEET-UI REPO - github.com/purpleworks/fleet-ui
FLEET VERSION - v0.11.5
BUILD DOCKER IMAGE VERSION - latest
...
```


## Run the fleet-ui on CoreOS

Here is an example of how to [Run fleet-ui on CoreOS.](https://github.com/xuwang/coreos-docker-dev/blob/master/README-fleet-ui.md)

## How does it work

The builder Dockerfile creates an isolated docker enviromment, with the required software packages and docker daemon installed. The result 
image is called _fleet-ui-builder_ which, when run, will build _fleet-ui_ docker image. _fleet-ui_ image is the one you will run or push to your dockerhub account to share. 

The builder environment includes:

* latest golang
* npm, ruby, gem etc.
* docker from Debian/Jessie release

The builder image can be removed when you are sure you have a good fleet-ui image: 

	docker rmi fleet-ui-builder:latest

## Troubleshoot

If you have problem with the fleet-ui, you can debug your build environment by run the builder image:

	docker run --rm -i -t fleet-ui-builder bash

The fleet-ui source code downloaded by Go is located at /gopath directory:

![fleet-ui src tree](https://github.com/xuwang/docker-fleetui-builder/blob/master/images/fleet-ui-src.png "fleet-ui src tree")

Then you can manually run steps in the builder Dockerfile for troubleshooting issues.
