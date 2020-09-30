FROM java:8

ENV DEBIAN_FRONTEND noninteractive
ENV ANDROID_HOME /opt/android-sdk-linux
ENV VERSION_SDK_TOOLS "4333796"

RUN dpkg --add-architecture i386

RUN apt-get update -qq \
    && apt-get install -y --no-install-recommends \
    libc6:i386 libstdc++6:i386 libgcc1:i386 libncurses5:i386 libz1:i386 net-tools \
    && rm -rf /var/lib/apt/lists/*

RUN cd /opt \
    && wget -q https://dl.google.com/android/repository/sdk-tools-linux-${VERSION_SDK_TOOLS}.zip -O android-commandline-tools.zip \
    && mkdir -p ${ANDROID_HOME} \
    && unzip -q android-commandline-tools.zip -d ${ANDROID_HOME} \
    && rm android-commandline-tools.zip


ENV PATH ${PATH}:${ANDROID_HOME}/platform-tools:${ANDROID_HOME}/tools/bin

RUN mkdir -p $ANDROID_HOME/licenses/ && \
    echo "8933bad161af4178b1185d1a37fbf41ea5269c55\nd56f5187479451eabf01fb78af6dfcb131a6481e" > $ANDROID_HOME/licenses/android-sdk-license && \
    echo "84831b9409646a918e30573bab4c9c91346d8abd" > $ANDROID_HOME/licenses/android-sdk-preview-license

RUN mkdir -p /root/.android && \
    touch /root/.android/repositories.cfg

RUN yes | sdkmanager --licenses

RUN yes | sdkmanager --update --channel=3

RUN yes | sdkmanager "emulator" "platform-tools"

RUN yes | sdkmanager \
    "platforms;android-29" \
    "build-tools;29.0.3" 

# ------------------------------------------------------
# --- Install Gradle from PPA

# Gradle PPA
ENV GRADLE_VERSION=6.1.1
ENV GRADLE_HOME=/opt/gradle/gradle-$GRADLE_VERSION
ENV PATH=$PATH:"/opt/gradle/gradle-${GRADLE_VERSION}/bin/":"/opt/gradlew/"
RUN wget https://services.gradle.org/distributions/gradle-${GRADLE_VERSION}-bin.zip -P /tmp \
    && unzip -d /opt/gradle /tmp/gradle-*.zip \
    && chmod +775 /opt/gradle \
    && gradle --version \
    && rm -rf /tmp/gradle*

RUN mkdir /opt/gradlew \
    && /opt/gradle/gradle-${GRADLE_VERSION}/bin/gradle wrapper --gradle-version ${GRADLE_VERSION} --distribution-type all -p /opt/gradlew  \
    && /opt/gradle/gradle-${GRADLE_VERSION}/bin/gradle wrapper -p /opt/gradlew

RUN /opt/gradlew/gradlew -v

# Cleaning
RUN apt-get clean
