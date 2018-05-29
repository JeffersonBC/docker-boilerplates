# Grails 3

This folder contains a clean Grails 3 project with a sample docker-compose file configured to run the project in a Docker container.

It has two separate Dockerfiles for development and production because Grails's production environment doesn't have live-reload when you change your code and it's actually necessary to rebuild the container's image whenever there's a change in the code, while the server used on the development environment isn't good enough to be used in real life.

If you are starting a new Grails project and want it running as a Docker container feel free to copy this folder, but if you already have a Grails project and wish to "dockerize" it then you are probably looking for the [docker-compose](https://github.com/JeffersonBC/docker-boilerplates/blob/master/grails/docker) files and for the [development](https://github.com/JeffersonBC/docker-boilerplates/blob/master/grails/app/Dockerfile) and [production](https://github.com/JeffersonBC/docker-boilerplates/blob/master/grails/app/Dockerfile.Prod) Dockerfiles.

### Prerequisites

The only prerequisite to run this project is to have Docker and Docker Compose installed as Grails itself will run from inside the container, although it's probably easier to have Grails installed in a machine used for development to use Grails CLI commands.

### Installing

In order to run the project for the first time just be in the same folder of the docker-compose file (either `docker/grails-prod` or `docker/grails-dev`) and execute `docker-compose up -d`. The first time you run it it will take a while to build the image as it needs to download all dependencies, but unless there are changes in the dependencies this step shouldn't take longer than an instant on the next executions.

### Development environment

After running the project once in the development environment it will feature live reload just like if you were running it in your machine without the docker layer between them. You can stop the container executing `docker-compose down` and run it again with `docker-compose up -d`.

One thing to notice is that in order to get changes in the `build.gradle` file (like new/ updated dependencies) reflected on your project it's necessary to rebuild the container image. For that you stop the container and re-run it by executing `docker-compose up -d --build`.

### Production environment

While building the production container's image, the project's source code is copied inside the container, compiled and then discarded, and only the compiled `.war` file remains.

Because of that workflow, every time you make a change to your code (and not just in the dependencies like the dev environment, although if there are no changes in dependencies rebuilding will be fast) it's necessary to rebuild the container's image and restart it. For that you just need to execute in the folder of the docker-compose file:

```
docker-compose up -d --build
```
