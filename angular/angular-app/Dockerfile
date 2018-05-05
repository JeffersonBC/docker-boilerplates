FROM node:8.11.1-alpine

# Copy package.json to inside the container and downloads all dependencies
WORKDIR /app

COPY package.json /app/
COPY package-lock.json /app/

RUN npm install

# Copy all the code and run the app
COPY . /app/
CMD [ "npm", "run-script", "start" ]
