# # Stage 0, "build-stage", based on Node.js, to build and compile the frontend
# FROM node:20 AS build-stage

# WORKDIR /app

# COPY package*.json /app/

# RUN npm install

# COPY ./ /app/

# ARG VITE_API_URL=${VITE_API_URL}

# RUN npm run build


# # Stage 1, based on Nginx, to have only the compiled app, ready for production with Nginx
# FROM nginx:1

# COPY --from=build-stage /app/dist/ /usr/share/nginx/html

# COPY ./nginx.conf /etc/nginx/conf.d/default.conf
# COPY ./nginx-backend-not-found.conf /etc/nginx/extra-conf.d/backend-not-found.conf


# Stage 0, "build-stage", based on Node.js, to build and compile the frontend
FROM node:20 AS build-stage

# Set the working directory inside the container
WORKDIR /app

# Copy only package.json and package-lock.json to leverage Docker caching for npm install
COPY package*.json /app/

# Install the dependencies
RUN npm install

# Copy the rest of the frontend source code
COPY ./ /app/

# Build arguments for API URL to pass to the Vite build (if needed)
ARG VITE_API_URL
ENV VITE_API_URL=${VITE_API_URL}

# Run the build command to create the production build
RUN npm run build


# Stage 1, based on Nginx, to serve the compiled app
FROM nginx:1

# Copy the built frontend files from the build stage to the Nginx web directory
COPY --from=build-stage /app/dist/ /usr/share/nginx/html

# Ensure the directory exists for additional Nginx config
RUN mkdir -p /etc/nginx/extra-conf.d

# Copy your Nginx configuration
COPY ./nginx.conf /etc/nginx/conf.d/default.conf
COPY ./nginx-backend-not-found.conf /etc/nginx/extra-conf.d/backend-not-found.conf

# Expose the default port that Nginx uses
EXPOSE 80

# Start Nginx
CMD ["nginx", "-g", "daemon off;"]
