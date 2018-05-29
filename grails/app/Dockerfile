# Dev image

FROM openjdk:8-jre-alpine

# Set Grails version and user configs
ARG GRAILS_VERSION=3.3.4

# Update alpine so wget works with https and to add the groupadd and useradd commands
RUN apk update
RUN apk add ca-certificates
RUN update-ca-certificates

# Instal Grails
WORKDIR /usr/lib/jvm

RUN wget https://github.com/grails/grails-core/releases/download/v$GRAILS_VERSION/grails-$GRAILS_VERSION.zip && \
    unzip grails-$GRAILS_VERSION.zip && \
    rm -rf grails-$GRAILS_VERSION.zip && \
    ln -s grails-$GRAILS_VERSION grails

ENV GRAILS_HOME /usr/lib/jvm/grails
ENV PATH $GRAILS_HOME/bin:$PATH

# Define the code and cache folders
RUN mkdir /app
RUN mkdir /cache
ENV GRADLE_USER_HOME /cache

# Download dependencies
# Create a new project and copies the build.gradle file so this step is cached unless dependencies change
WORKDIR /app
RUN grails create-app --inplace app --profile rest-api --features hibernate5

COPY build.gradle /app/
RUN grails dependency-report

# KEEP EVERYTHING UNTIL THIS STEP THE SAME ON BOTH DOCKERFILES SO EVERYTHING IS CACHED ============
# Copies the code to the container
COPY . /app

# Executa o app
CMD [ "grails", "run-app", "--stacktrace" ]
