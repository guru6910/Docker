#  ${\color{green} \textbf{Basic \ Docker \ Commands}}$ 

1. List Docker images:
````
   docker images
````
2. Pull Image From REgistry/DockerHub
````
docker pull <image_name>
````
3. Create Conatiner From Image
````
docker run -itd -p 80:80 <image_name>
````
4. List Running Conatainers
````
docker ps
````
5. List all stopped and Running Containers.
````
docker ps -a
````
6. Log In Into Conatainer
````
docker exec -it <container id> /bin/bash
````
7. Remove image
````
docker rmi <package_name>
````
8. Remove Container
````
docker rm <container_id>
````
9. Show the datail of Container
````
docker inspect <container_id>
````
10. Show logs of Container.
````
docker logs <container_id>
````
11. Stop Container
````
docker stop <container_id>
````
12. Start Container
````
docker start <container_id>
````
13. Stop all Running Containers
````
docker stop $(docker ps -qa)
````
14. Remove all running and stoppd containers
````
docker stop $(docker ps -a -q)
````
15. Remove all Stoppd containers
````
docker container prune
````
16. Build a image using Dockerfile
````
docker build -t <image_name>
````
17. Direct log in container working directory which we added in dockerfile.
````
docker run -it <image_name>
````
18. log in dockerhub in instance.
````
docker login
````
19. give tag to image for ppush on dockerhub
````
docker tag <image_name> guru6910/<image:tag>
````
20. push on dockerhub
````
docker push guru6910/<image:tag>
````
21. Remove all Images at once
````
docker rmi -f $(docker images)
````

## ${\color{red} \textbf{Docker Network}}$
22. List of Network.
````
docker network ls
````
23. Create Network
````
docker network create <network_name>
````
NOTE : Default Network type is bridge.

24. give Network type to network.
````
docker network create --driver <network_type> <Network_name>
````
25. Create container with Network & give name to container.
````
docker run -itd --network <network_name> --name <container_name> <image>
````
26. Delete Network.
````
docker network rm <network_id>
````
27. Create a volume.
````
docker volume create <volume_name>
````
28. list of docker volume
````
docker volume ls
````
29. Create container with volume with mount app in container.
````
docker run -itd --name <cont_name> --mount source=ebs,target=/app <image>
````
30. list of container whicch allowcated with particular volume.
````
docker ps --filter volume=<volume_name>
````
31. Delete all the container forcefully
````
 docker rm -f $(docker ps -qa)
````
