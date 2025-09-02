# Creating a docker
### A docker file is written in format of yml file and in Root directory
## existing file to a docker image
### 1 use an official nginx image to serve static content
FROM nginx:alpine
###  from nginx + alpine linux os docker image  
nginx is webserver and alpine is lightweight linux distributions

### set working directory
WORKDIR /usr/share/nginx/html
working directory of nginx html page

### remove all file in nginx static assaults
RUN rm -rf ./*
we need to remove all files in nginx html . inorder we can enter our folders

### copy static website files to nginx html directory
COPY index.html ./
COPY style.css ./
COPY assets ./assets 

copy our all files to that working directory
### expose port 80
EXPOSE 80
expose to port 80 http

### Start Nginx server
CMD ["nginx","-g","daemon off;"]
restarting the nginx server 

##  Describe over, next docker image creating and docker hub through push

### docker tag coffee swalihhh/coffee:v1
where cofee -- imagename
    swalihhh- dockerhub account name
    v1- version defining by us
(equals to git commit)    

### docker run -d --name coffee -p 8085:80 swalih/coffee
where -d - detach flag
    --name - container name defining (image name setting)
    -p -port
### port mapping    
    8085:host port (our port we can change according to our need)
    80:container port( no changes possible,its defined)

### docker ps 
can show all running images

### localhost :8085  
our website coffee is visiting


### docker exec -it 01ef /bin/sh
### Windows to alphine linux entering 
### light weight os alphine entering so only some linux commands can be running through it
