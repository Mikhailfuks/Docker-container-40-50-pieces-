tasks 40

FROM ubuntu:latest

# Install Java
RUN apt-get update && \
    apt-get install -y openjdk-11-jre-headless

# Set working directory
WORKDIR /opt/airsonic

# Download Airsonic-Advanced
RUN wget -nv https://github.com/airsonic-advanced/airsonic-advanced/releases/download/v10.11.3/airsonic-advanced-10.11.3.war -O airsonic-advanced.war

# Configure environment variables
ENV AIRSONIC_HOME /opt/airsonic
ENV AIRSONIC_LOG_LEVEL INFO
ENV AIRSONIC_DATABASE_URL jdbc:mysql://mysql:3306/airsonic?useUnicode=true&characterEncoding=utf8
ENV AIRSONIC_DATABASE_USER airsonic
ENV AIRSONIC_DATABASE_PASSWORD airsonic

# Install MySQL client
RUN apt-get install -y mysql-client

# Copy the startup script
COPY airsonic-startup.sh /opt/airsonic/

# Expose the web server port
EXPOSE 8080

# Start Airsonic-Advanced
CMD ["sh", "/opt/airsonic/airsonic-startup.sh"]

#!/bin/bash

# Start MySQL server if not already running
if ! pgrep -f "mysqld"; then
  echo "Starting MySQL server..."
  /etc/init.d/mysql start
fi

# Wait for MySQL to be available
while ! mysql -u root -h localhost -e 'SELECT 1'; do
  echo "Waiting for MySQL to become available..."
  sleep 1
done

# Create the database and user
mysql -u root -h localhost -e "CREATE DATABASE IF NOT EXISTS airsonic;"
mysql -u root -h localhost -e "GRANT ALL ON airsonic.* TO airsonic@localhost IDENTIFIED BY 'airsonic';"

# Run Airsonic-Advanced
java -Djava.awt.headless=true -jar airsonic-advanced.war &
wait

   mkdir airsonic-docker
   cd airsonic-docker
   
   docker build -t my-airsonic .
   
   docker run -d -p 8080:8080 --name my-airsonic my-airsonic
   
tasks 41 

FROM python:3.9-slim

WORKDIR /app

COPY requirements.txt .
RUN pip install --no-cache-dir -r requirements.txt

COPY FILE 

CMD ["pytest"]

docker build -t python-test .
docker run -it python-test

tasks 42 


    mkdir jellyfin-docker
    cd jellyfin-docker
    
    docker build -t my-jellyfin .
    
    docker build -t my-jellyfin .
    
    docker run -d -p 8096:8096 --name my-jellyfin my-jellyfin
    
        VOLUME ["/config", "/media"]
        
        EXPOSE 8080
        
        docker run -d -p 8080:8080 --name my-jellyfin my-jellyfin
        

        ENV Jellyfin_DatabaseProvider "MySql"
        ENV Jellyfin_DatabaseConnectionString "server=localhost;database=jellyfin;user=user;password=password"
        
tasks 43

FROM golang:1.17

WORKDIR /app

COPY . .

RUN go build -o main

CMD ["/app/main"]

package main

import (
  "fmt"
  "net/http"
)

func main() {
  http.HandleFunc("/", func(w http.ResponseWriter, r *http.Request) {
    fmt.Fprintf(w, "Hello from Go!")
  })

  fmt.Println("Server listening on port 8080")
  http.ListenAndServe(":8080", nil)
}

docker build -t go-app .
docker run -d -p 8080:8080 go-app

tasks 44

FROM python:3.9-slim

WORKDIR /app

COPY requirements.txt .
RUN pip install --no-cache-dir -r requirements.txt

COPY FILE 

CMD ["python", "app.py"]


from flask import Flask

app = Flask(__name__)

@app.route('/')
def hello_world():
    return 'Hello from Flask!'

if __name__ == '__main__':
    app.run(host='0.0.0.0', port=8080)

tasks 45 

FROM node:16-alpine

WORKDIR /app

COPY package*.json ./

RUN npm install

COPY FILE 

CMD ["npm", "start"]

trigger:
  - master

pool:
  vmImage: 'ubuntu-latest'

variables:
  DOCKER_HUB_USERNAME: 'your_docker_hub_username'
  DOCKER_HUB_PASSWORD: 'your_docker_hub_password'
  AZURE_SUBSCRIPTION_ID: 'your_azure_subscription_id'
  AZURE_RESOURCE_GROUP: 'your_azure_resource_group'
  AZURE_WEBAPP_NAME: 'your_azure_webapp_name'

steps:
- task: Docker@2
  inputs:
    command: 'build'
    containerImage: $(DOCKER_HUB_USERNAME)/node-app:latest
    dockerfile: 'Dockerfile'
- task: Docker@2
  inputs:
    command: 'push'
    containerImage: $(DOCKER_HUB_USERNAME)/node-app:latest
    registry: 'dockerhub'
    username: $(DOCKER_HUB_USERNAME)
    password: $(DOCKER_HUB_PASSWORD)
- task: AzureWebAppDeployment@2
  inputs:
    azureSubscription: $(AZURE_SUBSCRIPTION_ID)
    resourceGroupName: $(AZURE_RESOURCE_GROUP)
    appName: $(AZURE_WEBAPP_NAME)
    package: '$(System.DefaultWorkingDirectory)/app/build'


tasks 46 

FROM python:3.9-slim

ENV PYTHONUNBUFFERED=1

WORKDIR /app

COPY requirements.txt .
RUN pip install --no-cache-dir -r requirements.txt

COPY FILE 

CMD ["python", "manage.py", "runserver", "0.0.0.0:8000"]

version: '3.7'

services:
  web:
    build: .
    ports:
      - "8000:8000"
    depends_on:
      - db

  db:
    image: postgres:latest
    environment:
      POSTGRES_USER: myuser
      POSTGRES_PASSWORD: mypassword
      POSTGRES_DB: mydatabase
    ports:
      - "5432:5432"

docker-compose up -d


tasks 47 

version: '3.7'

services:
  web:
    build: .
    ports:
      - "80:80"
    depends_on:
      - db

  db:
    image: mysql:latest
    environment:
      MYSQL_ROOT_PASSWORD: mypassword
      MYSQL_DATABASE: mydatabase
      MYSQL_USER: myuser
      MYSQL_PASSWORD: mypassword
    ports:
      - "3306:3306"

docker-compose up -d

tasks 48 

FROM python:3.9-slim AS build

WORKDIR /app

COPY requirements.txt .
RUN pip install --no-cache-dir -r requirements.txt

COPY FILE 

FROM python:3.9-slim

WORKDIR /app

COPY --from=build /app .

CMD ["python", "app.py"]

import pandas as pd

print("Hello from Pandas!")

docker build -t python-pandas .
docker run -it python-pandas

FROM python:3.9-slim

WORKDIR /app

COPY requirements.txt .
RUN pip install --no-cache-dir -r requirements.txt

COPY FILE 

CMD ["pytest"]

docker build -t python-test .
docker run -it python-test

FROM tomcat:9.0-jre8

COPY target/*.war /usr/local/tomcat/webapps/

docker build -t java-app .
docker run -d -p 8080:8080 java-app


tasks 49 

version: '3.7'

services:
  web:
    build: .
    ports:
      - "80:80"
    environment:
      NODE_ENV: production
      APP_URL: http://app:3000
    depends_on:
      - app

  app:
    build: .
    ports:
      - "3000:3000"
    environment:
      DATABASE_URL: mysql://user:password@db:3306/database
    depends_on:
      - db

  db:
    image: mysql:latest
    environment:
      MYSQL_ROOT_PASSWORD: mypassword
      MYSQL_DATABASE: database
      MYSQL_USER: user
      MYSQL_PASSWORD: password
    ports:
      - "3306:3306"

      docker-compose up -d

tasks 50 

FROM php:7.4-apache

WORKDIR /var/www/html

COPY FILE 

CMD ["apachectl", "-D", "FOREGROUND"]

<?php

echo "Hello from PHP!";

?>

docker build -t php-app .
docker run -d -p 80:80 php-app







