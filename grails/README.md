# Grails 3

This folder contains a clean Grails 3 project with a sample docker-compose file configured to run the project in a Docker container.

It has two separate Dockerfiles for development and production because Grails's production environment doesn't have live-reload when you change your code and it's actually necessary to stop your container and rebuild its image whenever there's a change in the code, while the server used on the development environment isn't good enough to be used in real life.

Also, here I only use one docker-compose file to make it easier to see the difference between the enviroments, but it's probably a good idea to have two separate files, since with only one you need to edit it every time you want to go from one environment to the other, and with two files you just need to run the right one.

If you are starting a new Grails project and want it running as a Docker container feel free to copy this folder, but if you already have a Grails project and wish to "dockerize" it then you are probably looking for the [docker-compose.yml](https://github.com/JeffersonBC/docker-boilerplates/blob/master/grails/docker-compose.yml) file and for the [development](https://github.com/JeffersonBC/docker-boilerplates/blob/master/grails/app/Dockerfile) and [production](https://github.com/JeffersonBC/docker-boilerplates/blob/master/grails/app/Dockerfile-prod) Dockerfiles.

### Prerequisites

The only prerequisite to run this project is to have Docker and Docker Compose installed in your machine. Grails itself will run from inside the container.

### Installing

Before anything, you need to configure the UID and GID arguments in the `docker-compose.yml` file. They are necessary because Grails doesn't play so nice with Docker and this was a little workaround to avoid messing up with file permissions. You can find out what's the UID and GID of your current user by typing `echo $(id -u)` (for the UID) and `echo $(id -g)` (for the GID) on a unix bash.

With the UID and GID set, in order to run the project for the first time just be in the same folder of the docker-compose file and execute `docker-compose up -d`. The first time you run it it will take a while to download the dependencies, but unless there are changes in the dependencies this step won't take more than an instant on the next executions.

### Development environment

After you have run the project once in the development environment it will feature live reload just like if you were running it in your machine without the docker layer between them. You can stop the container executing `docker-compose down` and run it again with `docker-compose up -d`.

One thing to notice is that in order to get changes in dependencies described in the `build.gradle` file reflected on your project it's necessary to rebuild the container image. For that you stop the container and re-run it by executing `docker-compose up -d --build`.

### Production environment

When running the production environment it's a good idea to comment the volumes, since they are not necessary. During the image building the code is copied inside the container, compiled and then discarded, and only the compiled `.war` file has to remain.

Because of that workflow, every time you make a change to your code (and not just in the dependencies like the dev environment, although if there are no changes in dependencies rebuilding will be fast) it's necessary to stop the container (if it's running), rebuild the image and start it again. For that you just need to execute in the folder of the docker-compose file:

```
docker-compose down             // If the container is running
docker-compose up -d --build
```
