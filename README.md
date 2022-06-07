# spring-boot-angular
containerizing SpringBoot and Angular 

# Steps used in containerizing the Springboot Angular App

Prerequisites
Java JDK 16.0.1
Node 14.15.5
Angular CLI 1.7.4
Python v3.10.* (added to path)

# Spring Boot API
Install maven dependencies
run: mvn clean install

How to build
run: mvn clean package

How to run
run: mvn spring-boot:run

create a Dockerfile inside spring-boot-modules\spring-boot-angular\src\main\js\application folder with the configurations below
FROM openjdk:11
EXPOSE 8080
ADD target/spring-boot-angular.jar spring-boot-angular.jar
ENTRYPOINT ["java","-jar","/spring-boot-angular.jar"]

-build the docker image
docker build -t spring-boot-angular.jar .

run the docker image
docker run -p 8080:8080 spring-boot-angular.jar


#Angular Front End
This project was generated with Angular CLI version 1.7.4.

Install dependencies
Run npm install in the angular directory

Development server
Run ng serve for a dev server. Navigate to http://localhost:4200/. The app will automatically reload if you change any of the source files.

Build
Run ng build to build the project. The build artifacts will be stored in the dist/ directory. Use the -prod flag for a production build.

Create a docker-compose file inside spring-boot-modules\spring-boot-angular\src\main\js\application folder with the conf below
version: '3.5' # specify docker-compose version

#Define the services/containers to be run
services:
  angular-service: #name of the first service
    container_name: angular-container9
    build: ./ #specify the directory of the docker file
    volumes: # Volume binding
     - './:/usr/src/app'
    ports:
     - "4200:4200" # specify port forwarding
    command: >
     bash -c "npm start"
  docker-nginx:
    container_name: docker-nginxdocker 
    build: ./
    ports:
     # - "80:80"
    command: ["nginx", "-g", "daemon off;"]
    links:
      - angular-service
      
create a default configuration file inside spring-boot-modules\spring-boot-angular\src\main\js\application\src\nginx\etc\conf.d folder
server{
    listen 80
    server_name localhost;
    root /usr/share/ngix/html;
    index index.html index.html;
    location /api/users{
        proxy_pass http://localhost:8080/api/users;
    }

    location / {

        try_files $uri $uri /index.html;
    }
}

-build the docker-compose file
docker-compose up --build
