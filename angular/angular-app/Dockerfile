FROM teracy/angular-cli

# Copy package.json to inside the container and downloads all dependencies
WORKDIR /app
COPY package.json /app/
RUN npm install
RUN npm rebuild node-sass --force

# Copy all the code and run the app
COPY . /app
CMD [ "ng", "serve", "--host", "0.0.0.0" ]
