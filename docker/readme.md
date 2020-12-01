# Docker

## This is an example of a Project
- Hello World - Java, JavaScript and Python
- 2 Microservices - Currency Exchange and Currency Conversion

## Steps
- Step 01 - Docker and DevOps - Installation and Introduction
You can install docker on Windows, Linux or MAC (https://docs.docker.com).
The Virtualization mus be compatible and enabled.

- Step 02 - Your First Docker Usecase
For this example I install on an oracle linux on Oracle Cloud Plataform alweys free
(https://medium.com/oracledevs/run-always-free-docker-container-on-oracle-cloud-infrastructure-c88e36b65610.
	running python app:	sudo docker run -p 5000:5000 in28min/hello-world-python:0.0.1.RELEASE
	running java app: sudo docker run -p 5000:5000 in28min/hello-world-java:0.0.1.RELEASE
	running nodejs app: sudo docker run -p 5000:5000 in28min/hello-world-nodejs:0.0.1.RELEASE
	sudo docker ps (list)
	sudo docker stop "dockerid"
	
- Step 03 - Important Docker Concepts - Registry, Repository, Tag, Image and Container
	public Docker-hub: https://hub.docker.com
	ID: glaubersantos
	Link to the image: https://hub.docker.com/in28min/hello-world-nodejs
	Tags or version: 0.0.1.RELEASE
	Mapped to port: 5000(host port):5000(container port) 
	Runing image is called a docker
	
- Step 04 - Playing with Docker Images - Java, JavaScript and Python
	running multiples container in different ports:
	sudo docker run -p 5000:5000 in28min/hello-world-python:0.0.1.RELEASE
	sudo docker run -p 5001:5000 in28min/hello-world-java:0.0.1.RELEASE
	sudo docker run -p 5002:5000 in28min/hello-world-nodejs:0.0.1.RELEASE
	PS: Release the firewall on oracle vnc and use three linux terminal for each command.
	
- Step 05 - Playing with Docker - Detached Mode and Logs
	Detached mode (on background):
	sudo docker run -d -p 5002:5000 in28min/hello-world-nodejs:0.0.1.RELEASE
	
	Docker log (using the SHA): docker logs "33b785d80b463fd89308853846c554f3a818941c0450e9281d0fa2b0bc6b1968"
	Real time log: docker logs -f "33b785d80b463fd89308853846c554f3a818941c0450e9281d0fa2b0bc6b1968"
	
- Step 06 - Playing with Docker Images and Containers
	See images(all images): docker images
	See containers(only runings): docker container ls
	See containers history: docker container ls -a
	Stop a cotainer: stop container "SHA"

- Step 07 - Understanding Docker Architecture - Docker Client, Docker Engine
	Docker client (request)> Docker daemon (checks local registry and hub)> (Containers, local images, image registry)
	Example: 
	sudo docker run -d -p 5003:5000 in28min/hello-world-rest-api:0.0.1.RELEASE (remote)
	Not Works!
	docker log show that conianer is running on 8080
	docker ps > docker stop 
	sudo docker run -d -p 5003:8080 in28min/hello-world-rest-api:0.0.1.RELEASE (local)
	sudo docker run -d -p 9001:9001 r/etherpad/etherpad:latest
	Works!
		
- Step 08 - Understanding Docker Popularity - My 3 Top Reasons
	Standaridized App Packing - MultiPlataform-Light-Weight & Isolation
	
- Step 09 - Learning Docker Images - Commands
	See imges: docker images
	Pull latest image to be available locally (not run): docker pull "aplicationName"
	Check official images to use: docker search "aplicationName"
	Check all images of a repository: docker history "imageLink ou SHA"
	Details of an image: docker inspect "iamgeLink or SHA"	
	Remove an image: docker image remove "Link oer SHA"
		PS: no container must exist
	Remove container from local machine: docker container rm "SHA" (requires docker stop "SHA")
		
- Step 10 - Learning Docker Containers - Commands
	sudo docker run -d -p 5000:5000 in28min/hello-world-nodejs:0.0.1.RELEASE (short comand)
	or sudo docker container run -d -p 5003:8080 in28min/hello-world-rest-api:0.0.1.RELEASE (full comand)
	comand to list historical booted/exited containers: docker container ls -a
	Pause (freeze) a containner: docker container pause "SHA"
	Unpause the conainer: docker container unpause "SHA"
	Stop the conainer (poweroff - safe): docker container stop "SHA"
	Kill (force the shutdows - not safe) a container: docker container kill "SHA
	Logs check: docker container logs "SHA"
	List all details of a docker: docker container inspect "SHA"
	Remove all locally images of stoped containers: docker container prune
	
- Step 11 - Learning Docker Commands - system and stats
	List system parameters: docker system
	Check container disk usage: docker system df
	Evnts check: docker system events
	Remove all stopd conainers, networks not used, images not used, cahce: docker system prune -a
	status detailes: docker container stats "SHA"
	Specifies the limits for CPUs and memory to be used by docker:
	sudo docker run -d -p 5003:8080 -m 512m --cpu-quota=50000 in28min/hello-world-rest-api:0.0.1.RELEASE
	
- Step 12.1 - Importing Docker Project into Visual Studio Code
	Starting from a example
	Open the folder microservices in /devops/projects at glauberss2007 github using you VS Code
	For windows versions of docker you need top "Expose demon without TLS option"
	
- Step 12.2 - Building Docker Images for Python Application
	Simple example of a Docker file: 
	FROM python:alpine3.0 #Base Image
	WORKDIR /app
	COPY . /app	# copy requirements.txt (all the files) (files inside folder) to /app/files (requirements.txt)
	RUN pip install -r requirements.txt # Install (pip, npm,...) depedencys listed in requirements.txt (flask)
	EXPOSE 5000 # Specify the port to be exposed (5000)
	CMD python ./launch.py # Run this file
	Go to the folder with the files above and create the image: docker build -t in28min/hello-world-python:0.0.2.RELEASE .
	The image is created locally
	
- Step 13 - Understanding creation of Docker Images in Depth
		The process can be founded on the steps
	
- Step 14 - Pushing Python App Docker Image to Docker Hub
	Sign in on docker hub
	comando to login from host: docker login
	check the repository name: dockerID/projectname
	Push to remote hub: docker push dockerID/projectname:TAG
	Example: docker push in28min/hello-world-rest-api:0.0.1.RELEASE
	
- Step 15 - Building and Pushing Docker Image for Node JavaScript App
	Ope the project on github (glauberss2007) using VS Code
	FROM node:8.16.1-alpine #Base image
	WORKDIR /app	#working directory
	COPY . /app		#Cpy entire directory
	RUN npm install	#Install dependencys that app needs
	EXPOSE 5000		#Expode on port
	CMD node index.js	#File to run

	#ENTRYPOINT ["node", "index.js"]
	#COPY package.json /app
	
	Go to directory with the files and execute: docker build -t ... . (same of the step 12.2)
	
- Step 16 - Building and Pushing Docker Image for Java Application
	Creating an image of a spring boot app in java
	# Build a JAR File to run nin the image
	FROM maven:3.6.3-jdk-8-slim AS stage1 #"stage1" is the name of the place to be copied
	WORKDIR /home/app
	COPY . /home/app/
	RUN mvn -f /home/app/pom.xml clean package

	# Create an Image (copy the jar file to image)
	FROM openjdk:8-jdk-alpine
	EXPOSE 5000
	COPY --from=stage1 /home/app/target/hello-world-java.jar hello-world-java.jar #copy from the "stage1" or name seted above
	ENTRYPOINT ["sh", "-c", "java -jar /hello-world-java.jar"]
	
	Go to directory with the files and execute: docker build -t ... . (same of the step 12.2)
	This will take some mor time

- Step 17 - Building Efficient Docker Images - Improving Layer Caching
	Using the example of nojs docker file
		FROM node:8.16.1-alpine
		WORKDIR /app
		COPY pacjage.json /app		#copy only the files with the changes instead of all . for further version
		RUN npm install
		EXPOSE 5000
		CMD node index.js

	Improving speed to push/image generator by using cached for further tags on an image
	
	Teh same works for others projects
		
- Step 18 - Understanding ENTRYPOINT vs CMD
	Check a use of CMD(dockerfile) comand on docker projects using CMD (return the ping): 
	sudo docker container run -d -p 5003:8080 in28min/hello-world-rest-api:0.0.1.RELEASE ping google.com
	On project using ENTRYPOINT (dockerfile) the ping comand do not respond.
	
	So for static projects that the file to run will not change is recomended to use ENTRYPOINT 
	Fo dinamic projects that file to run can be changed is recomended use CMD
	
	Syntax examplo: 
	CMD node index.js
	ENTRYPOINT ["node", "index.js"]
		
- Step 19 - Docker and Microservices - Quick Start
	Microservices chain (hundred microservices)
	
- Step 20 - Introduction to Microservices - CE and CC
	Docker makes easier to deply
			Zero worry about deplyments
			Fewer Environment issues
			Easier operations (independent of plataforms includin orchestartors)

- Step 21 - Running Microservices as Docker Containers
	Concurrency Exchange and Concurrency Conversion
		Microservice 1 -> consult other microservice using an Environment_Variable -> Microservice 2
			
		Deploy CC->CE example: docker run -d -p 8000:8000 --name=currency-exchange in28min/currency-exchange:0.0.1-RELEASE
							   docker run -d -p 8000:8000 --name=currency-conversion in28min/currency-conversion:0.0.1-RELEASE
		
		Erro: CC cant conect to CE
		
- Step 22 - Using Docker Link to Connect Microservices
		List docker networks: docker network ls
		Inspect network: docker network inspect bridge
		
		By default bridges do not comunicate with each other
		Create a conect link among docker in a bridge(origem->target): docker run -d -p 8100:8100 --env CURRENCY_EXCHANGE_SERVICE_HOST=httpd://currency-exchange --name=currency-conversion --link currency-exchange in28min/currency-conversion:0.0.1-RELEASE 
		
- Step 23 - Using Custom Networking to Connect Microservices
	Configuring container with host network (hosts driver only comuniucate with linux plataform)
	Alternative creating a custom network: docker network create "currency-network"(name)
	Start or restart the dockers adding the new network: docker run -d -p 8000:8000 --name=currency-conversion --network=currency-network in28min/currency-conversion:0.0.1-RELEASE
		
- Step 24 - Using Docker Compose to Simplify Microservices Launch
	Make easier network configuration of containers:Install docker composer
	check version of composer: docker-compose --version
	Start composer at the same folder of "docker-compose.yml" file: docker-compose up
	Use an yml file to create a docker compose:
	Step 23 configuration example:
		version: '3.7'
		services:
		  currency-exchange:
			image: in28min/currency-exchange:0.0.1-RELEASE
			ports:
			  - "8000:8000"
			restart: always
			networks:
			  - currency-compose-network

		  currency-conversion:
			image: in28min/currency-conversion:0.0.1-RELEASE
			ports:
			  - "8100:8100"
			restart: always
			environment:
			  CURRENCY_EXCHANGE_SERVICE_HOST: http://currency-exchange
			depends_on:
			  - currency-exchange
			networks:
			  - currency-compose-network
  
# Networks to be created to facilitate communication between containers
networks:
  currency-compose-network:
	
- Step 25 - Understanding Docker Compose further
	run docker compose on detached mode: docker-compose up -d
	stop docker compose: docker-compose down
	Show config: docker-compose config
	Show images: docker-compose ...other comands similaries to docker

# Commands used

docker --version
docker run -p 5000:5000 in28min/hello-world-python:0.0.1.RELEASE
docker run -p 5000:5000 in28min/hello-world-java:0.0.1.RELEASE
docker run -p 5000:5000 in28min/hello-world-nodejs:0.0.1.RELEASE
docker run -d -p 5000:5000 in28min/hello-world-nodejs:0.0.1.RELEASE
docker run -d -p 5001:5000 in28min/hello-world-python:0.0.1.RELEASE
docker logs 04e52ff9270f5810eefe1f77222852dc1461c22440d4ecd6228b5c38f09d838e
docker logs c2ba
docker images
docker container ls
docker container ls -a
docker container stop f708b7ee1a8b
docker run -d -p 5001:8080 in28min/hello-world-rest-api:0.0.1.RELEASE
docker pull mysql
docker search mysql
docker image history in28min/hello-world-java:0.0.1.RELEASE
docker image history 100229ba687e
docker image inspect 100229ba687e
docker image remove mysql
docker image remove in28min/hello-world-java:0.0.1.RELEASE
docker container rm 3e657ae9bd16
docker container ls -a
docker container pause 832
docker container unpause 832
docker container stop 832
docker container inspect ff521fa58db3
docker container prune
docker system
docker system df
docker system info
docker system prune -a
docker top 9009722eac4d
docker stats 9009722eac4d
docker container run -p 5000:5000 -d -m 512m in28min/hello-world-java:0.0.1.RELEASE
docker container run -p 5000:5000 -d -m 512m --cpu-quota=50000  in28min/hello-world-java:0.0.1.RELEASE
docker system events

docker container stats 4faca1ea914e3e4587d1d790948ec6cb8fa34f26e900c12632fd64d4722fd59a
docker stats 42f170966ce613d2a16d7404495af7b3295e01aeb9142e1fa1762bbdc581f502

cd /in28Minutes/git/devops-master-class/projects/hello-world/hello-world-python 
docker build -t in28min/hello-world-python:0.0.2.RELEASE . 
docker run -p 5000:5000 -d in28min/hello-world-python:0.0.2.RELEASE
docker history e66dc383f7a0
docker push in28min/hello-world-python:0.0.2.RELEASE

cd ../hello-world-nodejs/
docker build -t in28min/hello-world-nodejs:0.0.2.RELEASE . 
docker container run -d -p 5000:5000 in28min/hello-world-nodejs:0.0.2.RELEASE
docker push in28min/hello-world-nodejs:0.0.2.RELEASE

cd ../hello-world-java/
docker build -t in28min/hello-world-java:0.0.2.RELEASE . 
docker run -d -p 5000:5000 in28min/hello-world-java:0.0.2.RELEASE
docker push in28min/hello-world-java:0.0.2.RELEASE

docker run -d -p 5001:5000 in28min/hello-world-nodejs:0.0.3.RELEASE ping google.com


docker run -d -p 8000:8000 --name=currency-exchange in28min/currency-exchange:0.0.1-RELEASE
docker run -d -p 8100:8100 --name=currency-conversion in28min/currency-conversion:0.0.1-RELEASE

docker network ls
docker network inspect bridge

docker run -d -p 8100:8100 --env CURRENCY_EXCHANGE_SERVICE_HOST=http://currency-exchange --name=currency-conversion --link currency-exchange in28min/currency-conversion:0.0.1-RELEASE

docker network create currency-network
docker container stop currency-exchange
docker container stop currency-conversion
docker run -d -p 8000:8000 --name=currency-exchange --network=currency-network in28min/currency-exchange:0.0.1-RELEASE
docker run -d -p 8100:8100 --env CURRENCY_EXCHANGE_SERVICE_HOST=http://currency-exchange --name=currency-conversion --network=currency-network in28min/currency-conversion:0.0.1-RELEASE

docker-compose --version
cd ../../microservices/
docker-compose up
docker-compose up -d
docker container ls
docker network ls
docker network inspect microservices_currency-compose-network
docker-compose down
docker container ls -a
docker system prune -a
docker-compose config
docker-compose images
docker-compose ps
docker-compose top

```
docker build -t in28min/hello-world-java:0.0.1.RELEASE .
docker push in28min/hello-world-java:0.0.1.RELEASE

docker build -t in28min/hello-world-python:0.0.1.RELEASE .
docker push in28min/hello-world-python:0.0.1.RELEASE

docker build -t in28min/hello-world-nodejs:0.0.1.RELEASE .
docker push in28min/hello-world-nodejs:0.0.1.RELEASE
```


