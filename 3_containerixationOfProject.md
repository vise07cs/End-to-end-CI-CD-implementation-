This app is using 22 microservices which is split in organization in real time  into multiple developement teams (1 team for payments , 1 for currency, 1 for other 3 services ..likewise )

As a devops engineer , you will work with 1 or 2 dev teams , in startups this number may go up . However in any case you might now work with all the microservices , unless the project is very small 




# Docker Life Cycle 

As a devops engineer our task is to containerize the microservices 

task --> conntainerize payment microservice   --> there are 3 stages 

1) setup docker file --> some info is needed , which is either present in Doccumentation or provided by developers 

2) create docker image  --> docker build 

3) create and run docker container  --> docker run 



# DEveloper and DevOps Collabration 

we will containerize the microservices which are written in GoLang , Java and Python 

product catalog service --> golang
Ad Service --> java
Recommendation Service --> python 


Process of conternization :

product catalog service --> golang

1) go to repo --> src --> product catalog --> readme 
When this service is run the output should be similar to the following

INFO[0000] Loaded 10 products                           
INFO[0000] Product Catalog gRPC server started on port: 8088 



To build the service binary, run:                   --> provided by developers in documentation / if not , then ask the developers 

export PRODUCT_CATALOG_PORT=<any-unique-port>
go build -o product-catalog . 


cd to ~/ultimate-devops-project-demo/src/product-catalog$




 export PRODUCT_CATALOG_PORT=8088


 go --version

sudo apt  install golang-go                 (go must be installed to run next command  )

go build -o product-catalog .           --> build dependencies is provided by develpoers in go.mod      (present in our github repo also , link below  )

ls 


(https://github.com/vise07cs/ultimate-devops-project-demo/blob/main/src/product-catalog/go.mod)  --> every prog lang has some dependencies 





./product-catalog   (executing product catalog )


ubuntu@ip-172-31-25-187:~/ultimate-devops-project-demo/src/product-catalog$ ./product-catalog       
INFO[0000] Loaded 10 products
INFO[0000] Product Catalog gRPC server started on port: 8088

we got the desired output --> the build process is successful 

we learnt , how to build the go binary locally

Now lets start with Docker Lifecycle



# containerization of 1st microservice (product catalog)

1) DOCKERfILE

always cd to the microservice you want to containerize 

cd //ultimate-devops-project-demo/src/product-catalog
ls
--> already has dockerfile --> delete it 

 rm -rf Dockerfile

git status  --> to verify if deleted 

ls

 rm -rf product-catalog       (delete product catalog --> optional )

ls 


vim Dockerfile 

FROM golang:1.22-alpine AS builder
WORKDIR /usr/src/app
COPY . .
RUN go mod download
RUN go build -o product-catalog ./

FROM alpine AS release
WORKDIR /usr/src/app
COPY ./products ./products
COPY --from=builder  /usr/src/app/product-catalog ./
EXPOSE ${PRODUCT_CATALOG_PORT}
ENV PRODUCT_CATALOG_PORT 8088
ENTRYPOINT ["./product-catalog"]



exit


 docker build -t vise1350/product-catalog:v1 .

now we need to validate if the created image is valid by runnig docker run command


 docker run vise1350/product-catalog:v1
time="2025-05-19T14:33:17Z" level=info msg="Loaded 10 products"
time="2025-05-19T14:33:17Z" level=info msg="Product Catalog gRPC server started on port: 8088"

--> we got the desired output which was mentioned in documentation 

created dockerifle
build docker image
execute the container 


------------------------------------------------------------------------------------------
# containerizing ad microservice (java)


cd to ad directory
ls

lets look at the instructions provided by the developer (in github repo )
link --> https://github.com/vise07cs/ultimate-devops-project-demo/blob/main/src/ad/README.md 

before doing docker containerization or implementing Docker lifecycle , its necessary that you know how to build the application locally 
because if you know how to build the app locally , it will help you greatly to write the dockerfile


from /src/ad/README.md (github)
The Ad service requires at least JDK 17 to build and uses gradlew to compile/install/distribute. Gradle wrapper is already part of the source code. To build Ad Service, run:

./gradlew installDist

<or>

./gradlew installDist -PprotoSourceDir=./proto


clearly , the build tool which is used here is gradle 



java --version   

sudo apt install openjdk-21-jre-headless    (install java )

./ gradlew

chmod +x ./gradlew    ---> (if permission issue comes )

./gradlew installDist    --> will start the gradle daemon/server , install dependencies , perform compilation , build the app which will be saved in  a particular directory (the directory info is already provided by the developer )




export AD_PORT=9099

export FEATURE_FLAG_GRPC_SERVICE_ADDR=featureflagservice:50053


./build/install/opentelemetry-demo-ad/bin/Ad               (executable )



the build is success , now lets create a multistage DockerFile using the same flow 


lets write DockerFIle 


rm -rf Dockerfile

vi Dockerfile

FROM eclipse-temurin:21-jdk AS builder
WORKDIR /usr/src/app/
COPY gradlew* settings.gradle* build.gradle .
COPY ./gradle ./gradle
RUN chmod +x ./gradlew
RUN ./gradlew
RUN ./gradlew downloadRepos

COPY . .
COPY ./pb ./proto
RUN chmod +x ./gradlew
RUN ./gradlew installDist -PprotoSourceDir=./proto

########################################################
FROM eclipse-temurin:21-jre
WORKDIR /usr/src/app
COPY --from=builder /usr/src/app/ ./
ENV AD_PORT 9099
ENTRYPOINT ["./build/install/opentelemetry-demo-ad/bin/Ad"]



save and exit :-


 docker build -t  vise1350/ad-service:v1 .


docker run vise1350/ad-service:v1                  --> running successfully (ad service started )








---------------------------------------------------------------------------------------------------------------------------------------------


# containerization of Recommendation Service  microservice (python )

cd /src/recommendation$

https://github.com/vise07cs/ultimate-devops-project-demo/blob/main/src/recommendation/README.md


the instructions are not provided by the developer in this case;

we have to ask developer to share the steps to run the application locally 

or 

you can try to understand the code by yourself 



























