#use an official nginx image to serve static content
FROM nginx:alpine

#set a working directory
WORKDIR /usr/share/nginx/html

#remove default nginx statis assets
RUN rm -rf ./*

#copy static website files to nginx html directory
COPY index.html ./
COPY style.css ./
COPY assets ./assets

#exposure port 80 
EXPOSE 80

#start nginx server
CMD ["nginx", "-g", "daemon off;"]