* create a multi stage build docker file for
    * 1. nop commerce
    * 2. spring pet clinic
    * 3. student courses register
* lets create amulti stage docker file to build nop commerce
      * lets create machine 
      * image.png
      * and install docker by using below commands
      * `curl -fsSL https://get.docker.com -o get-docker.sh`
             `sh get-docker.sh`
             `sudo usermod -aG docker docker` 
       * after installation lets try docker info
       * create a docker file for multi stage docker file for nop commerce
       *  ### Multi stage Docker file for NopCommerce
          FROM ubuntu:22.04 As builder
          RUN apt update && apt install unzip -y
          ADD https://github.com/nopSolutions/nopCommerce/releases/download/release-4.40.2/nopCommerce_4.40.2_NoSource_linux_x64.zip /nop/nopCommerce_4.40.2_NoSource_linux_x64.zip
          RUN cd nop && unzip nopCommerce_4.40.2_NoSource_linux_x64.zip && rm nopCommerce_4.40.2_NoSource_linux_x64.zip

          FROM mcr.microsoft.com/dotnet/sdk:7.0
          LABEL author="Prakash Reddy" organization="qt" project="learning"
          ARG HOME_DIR=/nop-bin
          WORKDIR ${HOME_DIR}
          COPY --from=builder /nop ${HOME_DIR}
          EXPOSE 5000
          CMD [ "dotnet", "Nop.Web.dll", "--urls", "http://0.0.0.0:5000" ]
       *  To build the docker image by using command `docker image build -t nop .`
       *  TO check the docker image by using command `docker image ls`  
       *  `image1.png`
       *  To run the docker container by using command ` docker container run --name nop -d -p 32000:5000 nop `      
       *  To check the container web page http://<publicip>:5000 and will get nop commerce
       *  `image2.png`


       * Push these images to azure container registry
       * `image3.png`

* multi stage docker file for building spring petclinic

### Multi stage Docker file for Spring-petclinic
FROM alpine/git AS vcs
RUN cd / && git clone https://github.com/spring-projects/spring-petclinic.git && \
    pwd && ls /spring-petclinic

FROM maven:3-amazoncorretto-17 AS builder
COPY --from=vcs /spring-petclinic /spring-petclinic
RUN ls /spring-petclinic 
RUN cd /spring-petclinic && mvn package

FROM amazoncorretto:17-alpine-jdk
LABEL author="rajasekhar" organization="qt" project="learning"
EXPOSE 8080
ARG HOME_DIR=/spc
WORKDIR ${HOME_DIR}
COPY --from=builder /spring-petclinic/target/spring-*.jar ${HOME_DIR}/spring-petclinic.jar
EXPOSE 8080
CMD ["java", "-jar", "spring-petclinic.jar"]

* vi dockerfile
* docker image build -t spc .
* image4.png
* image5.png
* docker container ls
* write a docker compose file for nop commerce

---
version: "3.9"
services:
  nop:
    build:
      context: .
      dockerfile: Dockerfile
    networks:
      - nop-net
    ports:
      - "32000:5000"
    depends_on:
      - nop-db
      
  nop-db:
    image: mysql:8
    networks:
      - nop-net
    volumes:
      - nop-db:/var/lib/mysql
    environment:
      - MYSQL_ROOT_PASSWORD=rajasekhar
      - MYSQL_USER=nop
      - MYSQL_PASSWORD=rajasekhar
      - MYSQL_DATABASE=nop
volumes:
  nop-db:
networks:
  nop-net:

* to run the docker container command `docker compose up -d`
* `image6.png`
* To check the container `docker container ls`
* `image7.png`
* To check the nopcommerce web page use the http://<publicip>:32000 (what even you given the opposite port of 32000:5000)
`image8.png`

*writing a docker compose file for spring pet clinic
---
version: "3.9"
services:
  spc:
    build:
      context: .
      dockerfile: Dockerfile
    networks:
      - spc-net
    ports:
      - "32000:8080"
    depends_on:
      - spc-db

  spc-db:
    image: mysql:8
    networks:
      - spc-net
    volumes:
      - spc-db:/var/lib/mysql
    environment:
      MYSQL_ROOT_PASSWORD: rajasekhar
      MYSQL_DATABASE: spc
      MYSQL_USER: spc
      MYSQL_PASSWORD: rajasekhar
volumes:
  spc-db:
networks:
  spc-net:


* Create a Dockerfile for Springpetclic application and insert that into vi Dockerfile

* And then write a docker-compose YAML file for Springpetclic application and insert that into vi Docker-compose.yaml

  

* To run the docker compose command is docker compose up -d
`image9.png`

         
* To check the docker container creation by using command docker container ls
`image10.png`

* Let's create a multi-stage docker file to build student courses register
* ### Multi stage Docker file for StudentCoursesRestAPI
FROM alpine/git AS vcs
RUN cd / && git clone https://github.com/DevProjectsForDevOps/StudentCoursesRestAPI.git && \
    pwd && ls /StudentCoursesRestAPI

FROM python:3.8.3-alpine As Builder
LABEL author="rajasekhar" organization="qt" project="learning"
COPY --from=vcs /StudentCoursesRestAPI /StudentCoursesRestAPI
ARG DIRECTORY=StudentCourses
RUN cd / StudentCoursesRestAPI cp requirements.txt /StudentCourses
ADD . ${DIRECTORY}
EXPOSE 8080
WORKDIR StudentCoursesRestAPI
RUN pip install --upgrade pip 
RUN pip install -r requirements.txt
ENTRYPOINT ["python", "app.py"]

* create a dockerfile in vi Dockerfile

* To build the docker image by using command docker image build -t StudentCoursesRestAPI .

`image11.png`

* To check the docker image creation by using command docker image ls
* To run the docker container by using command docker container run --name StudentCoursesRestAPI -d -p 32000:8080 StudentCoursesRestAPI:1.0 
* To check the docker container creation by using command docker container ls
* `image12.png`
* To check the StudentCoursesRestAPI web page use the http://<publicip>:30000 (what even you given the opposite port of 30000:8080)

* `image13.png`

* Write a docker compose file for StudentCoursesRestAPI.
* ---
version: "3.9"
services:
  pip:
    build:
      context: .
      dockerfile: Dockerfile
    ports:
      - "30000:8080"

* Create a Dockerfile for StudentCoursesRestAPI application and insert that into vi Dockerfile
* And then write a docker-compose YAML file for StudentCoursesRestAPI application and insert that into vi Docker-compose.yaml
* To run the docker compose command is docker compose up -d
* `image14.png`
* To check the docker container creation by using command docker container ls
* `image15.png`
* 


       



