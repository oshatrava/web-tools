FROM node:lts-fermium

#https://github.com/wv-gis/mudak-wrm-public/issues/2
ENV APT_KEY_DONT_WARN_ON_DANGEROUS_USAGE=DontWarn
ENV DEBIAN_FRONTEND noninteractive
ENV GRADLE_VERSION=4.8.1
ENV GRADLE_HOME=/opt/gradle
#Maven
ENV MAVEN_VERSION=3.6.3 \
    MAVEN_HOME=/usr/share/maven \
    MAVEN_CONFIG="$USER_HOME_DIR/.m2" \
    MAVEN_BASE_URL=https://downloads.apache.org/maven/maven-3

# Puppeteer
# https://github.com/adrianchia/docker-node-puppeteer/blob/master/12/Dockerfile
# https://github.com/GoogleChrome/puppeteer/blob/master/docs/troubleshooting.md#running-puppeteer-in-docker
# https://github.com/GoogleChrome/puppeteer/issues/290#issuecomment-322921352

RUN apt-get update && \
    apt-get install -yq apt-utils gconf-service libasound2 libatk1.0-0 libc6 libcairo2 \
    libcups2 libdbus-1-3 libexpat1 libfontconfig1 libgcc1 libgconf-2-4 libgdk-pixbuf2.0-0 \
    libglib2.0-0 libgtk-3-0 libnspr4 libpango-1.0-0 libpangocairo-1.0-0 libstdc++6 libx11-6 \
    libx11-xcb1 libxcb1 libxcomposite1 libxcursor1 libxdamage1 libxext6 libxfixes3 libxi6 mercurial rng-tools\
    libxrandr2 libxrender1 libxss1 libxtst6 libatk-bridge2.0-0 ca-certificates \
    fonts-liberation libappindicator1 libnss3 lsb-release xdg-utils wget --no-install-recommends \
    && wget -q -O - https://dl-ssl.google.com/linux/linux_signing_key.pub | apt-key add - \
    && sh -c 'echo "deb [arch=amd64] http://dl.google.com/linux/chrome/deb/ stable main" >> /etc/apt/sources.list.d/google.list' \
    && apt-get update \
    && apt-get install -y google-chrome-stable xvfb fonts-ipafont-gothic fonts-wqy-zenhei fonts-thai-tlwg fonts-kacst ttf-freefont \
      gtk2-engines-pixbuf xfonts-cyrillic xfonts-100dpi xfonts-75dpi xfonts-base xfonts-scalable imagemagick x11-apps openjdk-11-jdk \
          --no-install-recommends \
    && rm -rf /var/lib/apt/lists/* \
    #&& apt-get purge --auto-remove -y curl \
    && rm -rf /src/*.deb

# It's a good idea to use dumb-init to help prevent zombie chrome processes
ADD https://github.com/Yelp/dumb-init/releases/download/v1.2.5/dumb-init_1.2.5_x86_64 /usr/local/bin/dumb-init
RUN chmod +x /usr/local/bin/dumb-init

ENV DISPLAY=:99

# Java
# RUN apt-get install -yq openjdk-8-jdk && \  # installed on prev step
RUN java -version


# Maven
RUN cd /tmp \
    && mkdir -p /usr/share/maven \
    && wget "${MAVEN_BASE_URL}/${MAVEN_VERSION}/binaries/apache-maven-${MAVEN_VERSION}-bin.tar.gz" 2>/dev/null \
    && tar -xzf "apache-maven-${MAVEN_VERSION}-bin.tar.gz" -C /usr/share/maven --strip-components=1 \
    && ln -s /usr/share/maven/bin/mvn /usr/bin/mvn \
    && rm -rf /tmp/* \
    && mvn -version

# Gradle
RUN set -o errexit -o nounset \
	&& echo "Downloading Gradle" \
	&& wget --no-verbose --output-document=gradle.zip "https://downloads.gradle.org/distributions/gradle-${GRADLE_VERSION}-bin.zip" \
	&& echo "Installing Gradle" \
	&& unzip gradle.zip \
	&& rm gradle.zip \
	&& mv "gradle-${GRADLE_VERSION}" "${GRADLE_HOME}/" \
	&& ln -s "${GRADLE_HOME}/bin/gradle" /usr/bin/gradle

RUN set -o errexit -o nounset \
	&& echo "Testing Gradle installation" \
	&& gradle --version

# Sonar

WORKDIR /opt
RUN apt-get install -yq unzip && \
    curl -s -L https://binaries.sonarsource.com/Distribution/sonar-scanner-cli/sonar-scanner-cli-4.6.2.2472-linux.zip -o sonar.zip && \
    unzip -qq sonar.zip && \
    rm sonar.zip && \
    ln -s /opt/sonar-scanner-4.6.2.2472-linux/bin/sonar-scanner /usr/local/bin/sonar-scanner && \
    sonar-scanner --version
