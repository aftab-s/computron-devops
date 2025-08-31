#use an official nginx image to serve static content
FROM nginx:alpine

#set working directory
WORKDIR /usr/share/nginx/html
#remove all file in nginx static assaults
RUN rm -rf ./*
# copy static website files to nginx html directory
COPY index.html ./
COPY style.css ./
COPY assets ./assets 

#expose port 80
EXPOSE 80

# Start Nginx server
CMD ["nginx","-g","daemon off;"]