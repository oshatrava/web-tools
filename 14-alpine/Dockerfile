FROM node:14-alpine

# OpenJDK
ENV JAVA_HOME="/usr/lib/jvm/default-jvm/"
RUN apk update \
      && apk add --no-cache openjdk11 \
      && rm -rf /var/cache/apk/*

# Has to be set explictly to find binaries 
ENV PATH=$PATH:${JAVA_HOME}/bin

# Maven
RUN apk update \
      && apk add --no-cache maven \
      && rm -rf /var/cache/apk/*

# Gradle
RUN apk update \
      && apk add --no-cache gradle \
      && rm -rf /var/cache/apk/*
      
# Mercurial
RUN apk update \
      && apk add --no-cache mercurial \
      && rm -rf /var/cache/apk/*  
      
# rng-tools for FED Project
RUN apk update \
      && apk add --no-cache rng-tools \
      && rm -rf /var/cache/apk/*
      
# bash for MAA project
RUN apk update \
      && apk add --no-cache bash \
      && rm -rf /var/cache/apk/*

# Tell Puppeteer to skip installing Chrome. We'll be using the installed package.
ENV CHROME_BIN=/usr/bin/chromium-browser \
    PUPPETEER_EXECUTABLE_PATH=/usr/bin/chromium-browser \
    PUPPETEER_SKIP_CHROMIUM_DOWNLOAD=true

# Puppeteer
# https://github.com/adrianchia/docker-node-puppeteer/blob/master/12/alpine/Dockerfile
# https://github.com/GoogleChrome/puppeteer/blob/master/docs/troubleshooting.md#running-on-alpine

RUN apk update \
      && apk add --no-cache \
            chromium \ 
            nss \
            freetype \
            freetype-dev \
            harfbuzz \
            ca-certificates \
            ttf-freefont \
      && rm -rf /var/cache/apk/*


# Sonar
# https://community.sonarsource.com/t/installing-sonar-scanner-in-alpine-linux-docker/7010

WORKDIR /opt
RUN apk add --no-cache unzip curl && \
    curl -s -L https://binaries.sonarsource.com/Distribution/sonar-scanner-cli/sonar-scanner-cli-4.6.2.2472-linux.zip -o sonar.zip && \
    unzip -qq sonar.zip && \
    rm sonar.zip && \
    ln -s /opt/sonar-scanner-4.6.2.2472-linux/bin/sonar-scanner /usr/local/bin/sonar-scanner && \
    sed -i 's/use_embedded_jre=true/use_embedded_jre=false/g' /opt/sonar-scanner-4.6.2.2472-linux/bin/sonar-scanner && \
    sonar-scanner --version

# Git (needed for NPM install from Git)

RUN apk add --no-cache git

# Python (needed for NPM install Gyp)

RUN apk add --no-cache --virtual .gyp python make g++

#==============
# Show version
#==============
RUN java -version \
    && javac -version \
    && mvn -version \
    && node --version \
    && npm version \
    && yarn --version
