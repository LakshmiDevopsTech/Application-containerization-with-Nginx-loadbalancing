# Containerize IP location finding website and Nginx Load Balancing
## Description
In this project we've created a website for finding ip location for any IP. This is working by sending an API calls to ipstack.com. I've containerized this application using ipstack, redis and Nginx containers. Here redis is using for caching and Nginx is for loadbalancing
## Features
- All containers are build using a single file.
- A sample website also available for finding IP location. That creatd using Python
## Pre-Requests
- Install Docker and docker-compose
- Create a login in Ipstack login and API for website working
- Copy this API key to a .env file in the location of docker-compose file
- Create nginx.conf file in the location of docker-compose file
## Code Flow
- Nginx container creation (Nginx Loadbalancer)
- Redis container creation
- Three Ipstack container creation
- Network creation for the above Five containers
## Server pre-setup
Install Docker and docker-compose
```
yum install docker -y  <--- docker installation
------------------
sudo curl -L "https://github.com/docker/compose/releases/download/1.29.2/docker-compose-$(uname -s)-$(uname -m)" -o /usr/bin/docker-compose
sudo chmod +x /usr/bin/docker-compose
docker-compose version 
```
Create login on ipstack.com and get API from the `GET FREE API KEY`

![alt text](https://i.ibb.co/KFkzCKB/ipstack.png)

Then open the file `.env` in the docker-compose file locataion and copy this API
```
vim .env
IPSTACK_KEY=Enter the API key here
```
create nginx.conf file 
```
#vi nginx.conf
events {
    worker_connections   1000;
}

http {
   upstream backend {
     server ipstack1;
     server ipstack2;
     server ipstack3;
   }

   server {

      listen 80;
      location / {
          proxy_pass http://backend;
      }
   }
}
```
## Behind the playbook
Important parts only mentioning here, for more details please check [docker-compose.yml](https://github.com/LakshmiDevopsTech/Application-containerization-with-Nginx-loadbalancing/blob/main/docker-compose.yml)
```

  nginx:                 <--------------- Nginx container
    image: nginx:alpine
    container_name: nginx
    restart: always
    ports:
      - "80:80"
    networks:
      - ipstacknet
    volumes:
      - ./nginx.conf:/etc/nginx/nginx.conf

  redis:                                 <--------redis container
    image: redis:latest
    container_name: redis
    restart: always
    networks:
      - ipstacknet

  ipstack1:                               <-------- Ipstack server 1
    image: lakshmidevopstech/ipstack:latest
    container_name: ipstack1
    restart: always
    depends_on:
      - redis   <----------- For caching using redis container
    networks:
      - ipstacknet
    environment:
      CACHING_SERVER: redis
      IPSTACK_KEY: "${IPSTACK_KEY}" <------- it will take key from .env file

  ipstack2:                          <------------ Ipstack server 2
    image: lakshmidevopstech/ipstack:latest
    container_name: ipstack2
    restart: always
    depends_on:
      - redis
    networks:
      - ipstacknet
    environment:
      CACHING_SERVER: redis
      IPSTACK_KEY: "${IPSTACK_KEY}"

  ipstack3:                           <----------- Ipstack server 3
    image: lakshmidevopstech/ipstack:latest
    container_name: ipstack3
    restart: always
    depends_on:
      - redis
    networks:
      - ipstacknet
    environment:
      CACHING_SERVER: redis
      IPSTACK_KEY: "${IPSTACK_KEY}"

networks:
  ipstacknet:
```
## Architecture
![alt text](https://i.ibb.co/VTK9Pb4/proxy.png)
## Output
The output be like :
![alt_text](https://i.ibb.co/Z2vj2Vx/result.png)
## Conclusion
Its a sample application for understanding the working of containers with loadbalancer. Also we can do this using AWS load application load balancer also.
### ⚙️ Connect with Me

<p align="center">
<a href="mailto:lakshmipriya458@gmail.com"><img src="https://img.shields.io/badge/Gmail-D14836?style=for-the-badge&logo=gmail&logoColor=white"/></a>
<a href="https://www.linkedin.com/in/lakshmipriya-p-c-7b5a2b88/"><img src="https://img.shields.io/badge/LinkedIn-0077B5?style=for-the-badge&logo=linkedin&logoColor=white"/></a>  
