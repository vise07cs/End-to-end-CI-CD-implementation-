# Intro to docker Compose


suppose all the 3 microservice are created by a developement team and you as a devops engineer cntainerized these services and pushed the images to dockerHub

anyone can pull these images and run using docker run and verify the changes made by the developer is valid or not 

but the developers / qe engineers complained that even to run these docker commands thaey have to provision many things and write 10-15 commands 
They have to provision :-

docker network 
docker volumes 
pull images 
run (docker run)
and containers must run in a sequence


For this Docker Compose comes into picture 

Devops engineeer will create a dockerCompose.yaml file and this yaml file will have all the steps to run the environment 

writing docker compose file :-
(keep 3 things in mind)
1) create a .yaml file -->contains  3 objects --> services , networks , volumes 
    services --> define microservices (how to pull and run microservices)
    network ---> creating network for all the microservices
    volumes --> optional ()

    link --> https://github.com/vise07cs/ultimate-devops-project-demo/blob/main/docker-compose.yml 
    the above file contains docker compose commands for all the 22 microservices , just go through them once

    cd to the projetc
    ls
    docker compose down --> will stop and remove the docker containers 
    docker compose up -d --> will re run the conatainers 

    check on the browser --> publicIp:8080 --> Everything (every microservice) working fine


    
     