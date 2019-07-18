FROM jenkins/jenkins:lts

MAINTAINER Berat Onur Ersen <onurersen@gmail.com>

USER root

RUN apt-get install -y wget
RUN apt-get install -y git

RUN wget --no-verbose -O /tmp/apache-maven-3.6.1-bin.tar.gz http://ftp.itu.edu.tr/Mirror/Apache/maven/maven-3/3.6.1/binaries/apache-maven-3.6.1-bin.tar.gz

RUN tar xzf /tmp/apache-maven-3.6.1-bin.tar.gz -C /opt/
RUN ln -s /opt/apache-maven-3.6.1 /opt/maven
RUN ln -s /opt/maven/bin/mvn /usr/local/bin
RUN rm -f /tmp/apache-maven-3.6.1-bin.tar.gz
ENV MAVEN_HOME /opt/maven

RUN chown -R jenkins:jenkins /opt/maven

RUN apt-get clean

USER jenkins
