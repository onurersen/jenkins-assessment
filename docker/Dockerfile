FROM jenkins/jenkins:lts

MAINTAINER Berat Onur Ersen <onurersen@gmail.com>

USER root

RUN apt-get install -y wget 

RUN wget --no-verbose -O \ 
	/tmp/apache-maven-3.6.1-bin.tar.gz http://ftp.itu.edu.tr/Mirror/Apache/maven/maven-3/3.6.1/binaries/apache-maven-3.6.1-bin.tar.gz && \
	tar xzf /tmp/apache-maven-3.6.1-bin.tar.gz -C /opt/ && \
	ln -s /opt/apache-maven-3.6.1 /opt/maven && \
	ln -s /opt/maven/bin/mvn /usr/local/bin && \
 	rm -f /tmp/apache-maven-3.6.1-bin.tar.gz

ENV MAVEN_HOME /opt/maven

RUN chown -R jenkins:jenkins /opt/maven

RUN	apt-get clean && \ 
	apt-get autoremove && \
	apt-get remove -y wget

USER jenkins
