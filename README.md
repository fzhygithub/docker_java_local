…or create a new repository on the command line echo "# docker_build_java" >> README.md git init git add README.md git commit -m "first commit" git remote add origin https://github.com/fzhygithub/docker_build_java.git git push -u origin master

…or push an existing repository from the command line git remote add origin https://github.com/fzhygithub/docker_build_java.git git push -u origin master

…or import code from another repository You can initialize this repository with code from a Subversion, Mercurial, or TFS project.


#
# MAINTAINER        fanzhengyuan <6562157@qq.com>
# DOCKER-VERSION    Docker version 17.04.0-ce, build 4845c56
#
# Dockerizing maven: Dockerfile for building maven images
#
FROM       jdk:1.8.0
MAINTAINER fanzhengyuan <6562157@qq.com>

ENV MAVEN_VERSION 3.5.0
ENV MAVEN_HOME /opt/maven

# Install maven
COPY apache-maven-3.5.0-bin.tar.gz /opt/

RUN tar xzf /opt/apache-maven-3.5.0-bin.tar.gz -C /opt && \
    mv /opt/apache-maven-${MAVEN_VERSION} /opt/maven  && \
    ln -s /opt/maven/bin/mvn /usr/bin/mvn

COPY settings.xml /opt/maven/conf/settings.xml


### Tomcat ###
ENV CATALINA_HOME /usr/local/tomcat7
ENV PATH $CATALINA_HOME/bin:$PATH
#ENV TOMCAT_MAJOR 7
#ENV TOMCAT_VERSION 7.0.96
#ENV TOMCAT_TGZ_URL https://www.apache.org/dist/tomcat/tomcat-$TOMCAT_MAJOR/v$TOMCAT_VERSION/bin/apache-tomcat-$TOMCAT_VERSION.tar.gz
#RUN curl -SL https://archive.apache.org/dist/tomcat/tomcat-$TOMCAT_MAJOR/v$TOMCAT_VERSION/bin/apache-tomcat-$TOMCAT_VERSION.tar.gz -o /tmp/tomcat.tar.gz 

COPY apache-tomcat-7.0.96.tar.gz /tmp/tomcat.tar.gz
RUN tar -xvf /tmp/tomcat.tar.gz -C /usr/local/ \
  && ln -s /usr/local/apache-tomcat-7.0.96 $CATALINA_HOME  \
  && rm -rf /tmp/tomcat.tar.gz

ADD hello /hello
RUN cd /hello && \
    mvn clean install -Dmaven.wagon.http.ssl.insecure=true -Dmaven.wagon.http.ssl.allowall=true


RUN cp /hello/target/hello.war /usr/local/apache-tomcat-7.0.96/webapps/

### run ###
EXPOSE 8080
CMD ["catalina.sh", "run"]


#EXPOSE 8080
#CMD ["java","-jar","/hello/target/hello.war"]


#https://github.com/fzhygithub/docker_build_java.git
#docker run -d -p 8888:8080 java:latest
#curl http://localhost:8888/hello/index.jsp
