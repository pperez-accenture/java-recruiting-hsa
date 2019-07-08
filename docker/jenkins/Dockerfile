# Starting off with the Jenkins base Image
FROM jenkins/jenkins:latest

#Before continue, update.
USER root
RUN apt-get update && apt-get install -y --no-install-recommends apt-utils

# Turn back to Jenkins
USER jenkins

#Copy all the necessary elements.
COPY jobs /usr/share/jenkins/ref/jobs
COPY workspace /usr/share/jenkins/ref/workspace
COPY credentials.xml /usr/share/jenkins/ref/credentials.xml

# Start-up scripts to set number of executors and creating the admin user
COPY addTomcatCredential.groovy /usr/share/jenkins/ref/init.groovy.d/

# Add/Remove  the plugins you want  
COPY plugins.txt /usr/share/jenkins/ref/plugins.txt
RUN /usr/local/bin/install-plugins.sh < /usr/share/jenkins/ref/plugins.txt
 
# Setting up environment variables for Jenkins admin user
ENV JENKINS_USER admin
ENV JENKINS_PASS admin
 
# Skip the initial setup wizard
ENV JAVA_OPTS -Djenkins.install.runSetupWizard=false

# Start-up scripts to set number of executors and creating the admin user
COPY default-user.groovy /usr/share/jenkins/ref/init.groovy.d/

# Install Flutter as ROOT
USER root
RUN apt-get update && apt-get install -y --force-yes \
	curl xz-utils git wget unzip libgconf-2-4 gdb libstdc++6 libglu1-mesa fonts-droid-fallback lib32stdc++6 python3 \
    && apt-get clean \
    && git clone -b stable https://github.com/flutter/flutter.git /usr/local/flutter \
    && chown -R jenkins:jenkins /usr/local/flutter
	

# Turn back to Jenkins again
USER jenkins

# Running flutter doctor to check if flutter was installed correctly
RUN /usr/local/flutter/bin/flutter doctor -v \
    && rm -rfv /flutter/bin/cache/artifacts/gradle_wrapper

# Setting flutter and dart-sdk to PATH so they are accessible from terminal
ENV PATH="/usr/local/flutter/bin:/usr/local/flutter/bin/cache/dart-sdk/bin:${PATH}"

VOLUME $JENKINS_HOME