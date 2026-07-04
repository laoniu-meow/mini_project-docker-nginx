# Stage 1: Build the Vite application
# FIX: Fixed typo "alphine" to "alpine"
FROM node:24-alpine AS build-stage

# Set a working directory in the container
WORKDIR /app

# Copies package.json and package-lock.json from local into /app inside the Docker container
# NOTE: Make sure your folder name is exactly "myApp" (case-sensitive)
COPY myApp/package*.json ./

# Install npm inside the Docker container
RUN npm install

# Copies everything from your local project directory (the build context) into /app
# FIX: Changed ".." to "myApp/ ." so it correctly copies your source files from the build context
COPY myApp/ .

# To prepare your application for production
RUN npm run build 

# Stage 2: Serve the app with Nginx
# Start building this image from the latest Nginx image
FROM nginx:1.29-alpine

# Copy our custom Nginx config
# FIX: Removed the "../" prefix. Because the build context is ".", Docker naturally sees the local folders.
COPY /nginx/nginx.conf /etc/nginx/conf.d/default.conf

# Copy our SSL certificates
COPY /nginx/ssl /etc/nginx/ssl

# Copy compiled static Vite files into Nginx default serving path
# FIX: Added "html" to the end of the destination path so Nginx knows where to find the index file.
COPY --from=build-stage /app/dist /usr/share/nginx/html

# Defined Port to tell docker which port to listen
EXPOSE 80 443

# Command to run when the container starts
CMD ["nginx", "-g", "daemon off;"]