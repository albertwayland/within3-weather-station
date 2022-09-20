# within3-weather-station
# Add a line in our Dockerfile that tells Docker what base image we would like to use for our application.
  FROM node:12.18.1
# The NODE_ENV environment variable specifies the environment in which an application is running (usually, development or production.
  ENV NODE_ENV=production
# create a working director
  WORKDIR /app
# Before we can run npm install, we need to get our package.json files into our images.
  COPY ["package.json" "./"]
# RUN command to execute the command npm install
  RUN npm install --production
# Add source code into the image. 
  COPY . .
# Expose application port
  EXPOSE 8080
# Run the image inside of the container with the CMD command.
  CMD [ "node", "index.js" ]
# Build the Docker image
  docker build --tag node-docker .
# Test and deploy the container locally
  docker run --rm -d  -p 8080:8080/tcp node-docker:latest
