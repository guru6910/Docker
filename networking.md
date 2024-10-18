
## COMMUNICATE BETWEEN TWO CONTAINER USING BRIDGE NETWORK ##

1. docker pull httpd   ->   pull httpd image from docker hub 
2. docker network ls 
3. docker network create --network bridge vnet   ->   create a network with the type of Bridge
4. docker network run --network vnet --name cont-1 httpd    ->   create a container cont-1
5. docker network run --network vnet --name cont-2 httpd   ->   create a container cont-2
6. docker exec -it cont-1   ->   log in to the cont-1
7. apt update -y 
8. apt install iputils-ping   ->   install iputils-ping for communication
9. ping cont-2    ->   ping and exit from cont-1
10. docker exec -it cont-2   ->   log in to the cont-2
11. apt update -y 
12. apt install iputils-ping   ->   install iputils-ping for communication
13. ping cont-1
