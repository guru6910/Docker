### Install Docker 
````
sudo yum update -y
sudo yum install -y docker
sudo service docker start
sudo usermod -a -G docker ec2-user
````


### HTTPD Image From dockerhub
````
docker pull httpd
docker images
docker run -itd --name httpd-server -p 81:80 httpd
docker ps 
````


### NGINX Image From dockerhub
````
docker pull nginx
docker images
docker run -itd --name nginx-server -p 82:80 nginx
docker ps 
````


### TOMCAT Image From dockerhub 
````
docker pull tomcat 
docker images
docker run -itd --name tomcat-server -p 83:8080 tomcat
docker ps 
docker exec -it <container_id> /bin/bash
cd /webapps.dist
cp -r * /usr/local/tomcat/webapps/
````


## Build a image and run using "Dockerfile"

### HTTPD 
````
vim Dockerfile
````
````
 FROM httpd:latest
````
````
docker images
docker build -t httpd
docker run -itd --name httpd-server -p 81:80 httpd
docker ps 
````


### NGINX
````
vim Dockerfile
````
````
FROM ubuntu:20.04
````
````
  WORKDIR Guru
  RUN apt update -y && \
      apt install nginx -y

  COPY index.html /var/www/html/
  CMD ["nginx","-g","daemon off;"]  
````
````
echo "HELLO GURU" > index.html
docker build -t nginx .
docker run -itd --name nginx-server -p 81:80 nginx
docker ps 
````

### TOMCAT
````
vim Dockerfile
````
````
   FROM tomcat:latest
   LABEL maintainer="your-email@example.com"
   CMD ["catalina.sh", "run"]
````
````
docker images
docker run -itd --name tomcat-server -p 83:8080 tomcat
docker ps 
docker exec -it <container_id> /bin/bash
cd /webapps.dist
cp -r * /usr/local/tomcat/webapps/
````

## COMMUNICATE BETWEEN TWO CONTAINER USING BRIDGE NETWORK ##

1. docker pull httpd -> pull httpd image from docker hub 
2. docker network ls 
3. docker network create --network bridge vnet -> create a network with the type of Bridge
4. docker network run --network vnet --name cont-1 httpd -> create a container cont-1
5. docker network run --network vnet --name cont-2 httpd -> create a container cont-2
6. docker exec -it cont-1 -> log in to the cont-1
7. apt update -y 
8. apt install iputils-ping -> install iputils-ping for communication
9. ping cont-2  -> ping and exit from cont-1
10. docker exec -it cont-2 -> log in to the cont-2
11. apt update -y 
12. apt install iputils-ping -> install iputils-ping for communication
13. ping cont-1

## Docker volume task : modify one container and see that result another container which mounted with same volume.


**docker volume**
```
docker volume create ebs-vol
docker volume ls
docker run -itd --name cont1  --mount source=ebs,target=/app ubuntu:latest
docker run -itd --name cont2  --mount source=ebs,target=/app ubuntu:latest
docker ps --filter volume=ebs-vol
docker exec -it cont1 /bin/bash
cd app
touch demo.txt
exit
#### log in into another continer
docker exec -it cont2 /bin/bash
ls app
````
#### attach local to file to container
````
docker run -itd --name example1 -v ${PWD}/ec2:/var/demo ubuntu
````
