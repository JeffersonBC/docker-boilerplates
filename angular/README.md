# Angular

This folder contains a clean Angular project with a sample docker-compose file configured to run the project in a Docker container.

Angular has a nice development environment with live-reload, but the server used for that isn't very good to be used in production, so there are two Dockerfiles and docker-compose.yml files, each having one file for dev and one for prod environments.

If you are starting a new Angular project and want it running in Docker this folder is a good start point, but if you already have a project and just wish to "dockerize" it then you need the docker-compose.yml files at the [docker folder](https://github.com/JeffersonBC/docker-boilerplates/blob/master/angular/docker/), the [development](https://github.com/JeffersonBC/docker-boilerplates/blob/master/angular/angular-app/Dockerfile) and [production](https://github.com/JeffersonBC/docker-boilerplates/blob/master/angular/angular-app/Dockerfile.Prod) Dockerfiles and the [Nginx configuration file](https://github.com/JeffersonBC/docker-boilerplates/blob/master/angular/angular-app/nginx-custom.conf), just remember to ajust the file paths in the docker-compose files to suit your project.

### Prerequisites

The only prerequisite to run this project is to have Docker and Docker Compose installed in your machine, Angular itself will run from inside the container. That being said, it's probably easier to have Angular CLI installed in your machine to work in the dev environment (alhtough it's possible to just run `ng` commands inside the container, using the container's Angular CLI installation).

### Installing

Considering you have all the files in the right folders, there's nothing to do except to run `docker-compose up -d`. The first time you run it it will take a while to download the dependencies, but unless there are changes in the dependencies this step won't take more than an instant on the next executions, even if you rebuild the image.

Both the development and production environmens serve the Angular app in [localhost:4200](http://localhost:4200/).

### Development environment

The development environment features live reload just like if you were running it in your machine without the docker layer between them. You can stop the container executing `docker-compose down` and run it again with `docker-compose up -d`.

One thing to notice is that in order to get changes in dependencies described in the `package.json` file reflected on your project it's necessary to rebuild the container image. For that you stop the container and re-run it by executing `docker-compose up -d --build` (the `-d` flag is to not append the output logs of the containers to your bash).

### Production environment

Unlike the development environment there's no live reload here. The production environment works by creating a Docker image with node installed, compiling the code and then sending that compiled code to another image with just Nginx installed, which is used to serve the static files.

This is done to keep the size of the production container only as big as necessary, but because of that, after every change in code, it's necessary to run:

```
docker-compose down             // If the container is running
docker-compose up -d --build
```

This will rebuild the image and put the app back online.
