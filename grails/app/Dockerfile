FROM openjdk:8-jre

# Versão Grails
ENV GRAILS_VERSION 3.3.4

# Instala o Grails
WORKDIR /usr/lib/jvm

RUN wget https://github.com/grails/grails-core/releases/download/v$GRAILS_VERSION/grails-$GRAILS_VERSION.zip && \
    unzip grails-$GRAILS_VERSION.zip && \
    rm -rf grails-$GRAILS_VERSION.zip && \
    ln -s grails-$GRAILS_VERSION grails

ENV GRAILS_HOME /usr/lib/jvm/grails
ENV PATH $GRAILS_HOME/bin:$PATH

# Cria usuário
RUN groupadd user &&\
    useradd -g user -d /home/user -s /sbin/nologin -c "Docker image user" user

# Cria e popula diretório de trabalho, dá permissão ao novo usuário e loga como o novo usuário
RUN mkdir /app
COPY . /app
WORKDIR /app

# Define pasta para cache
RUN mkdir /cache
ENV GRADLE_USER_HOME /cache

# Inicia a aplicação
RUN grails compile

RUN chown -R user:user /app
RUN chown -R user:user /cache

USER user

CMD [ "grails", "run-app", "--stacktrace" ]
