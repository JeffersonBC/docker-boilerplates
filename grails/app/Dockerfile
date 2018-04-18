# Dev image

FROM openjdk:8-jre

# Set Grails version
ARG GRAILS_VERSION=3.3.4

# Instala o Grails
WORKDIR /usr/lib/jvm

RUN wget https://github.com/grails/grails-core/releases/download/v$GRAILS_VERSION/grails-$GRAILS_VERSION.zip && \
    unzip grails-$GRAILS_VERSION.zip && \
    rm -rf grails-$GRAILS_VERSION.zip && \
    ln -s grails-$GRAILS_VERSION grails

ENV GRAILS_HOME /usr/lib/jvm/grails
ENV PATH $GRAILS_HOME/bin:$PATH

# Copy the app's folder to /app inside the container
RUN mkdir /app
COPY . /app
WORKDIR /app

# Define the cache folder
RUN mkdir /cache
ENV GRADLE_USER_HOME /cache

# Compile the app
RUN grails compile

# Create a new user with the UID and GID provided by the docker-compose file,
# or use 1000 as default if nothing was provided
ARG UNAME=user
ARG UID=1000
ARG GID=1000

RUN groupadd -g $GID $UNAME && \
    useradd -u $UID -g $UNAME -d /home/$UNAME -s /sbin/nologin -c "Docker image user" $UNAME

RUN chown -R $UNAME:$UNAME /app
RUN chown -R $UNAME:$UNAME /cache

USER $UNAME

# Run the app
CMD [ "grails", "run-app", "--stacktrace" ]
