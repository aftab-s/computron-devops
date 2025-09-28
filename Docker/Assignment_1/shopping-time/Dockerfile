# Stage 1: Build React app
FROM node:18-alpine AS build

WORKDIR /app

# Copy package.json and package-lock.json
COPY package*.json ./

# Install dependencies (clean cache after to keep image small)
RUN npm install --legacy-peer-deps && npm cache clean --force

# Copy all files
COPY . .

# Build React app
RUN npm run build


# Stage 2: Serve with Nginx
FROM nginx:alpine

# Remove default nginx static files
RUN rm -rf /usr/share/nginx/html/*

# Copy build output to Nginx html folder
COPY . .

# Copy a custom Nginx config (optional, for React Router / SPA)
# COPY nginx.conf /etc/nginx/conf.d/default.conf

# Expose port 80
EXPOSE 80

# Run Nginx in foreground
CMD ["nginx", "-g", "daemon off;"]
