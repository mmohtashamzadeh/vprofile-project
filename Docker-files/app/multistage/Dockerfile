FROM eclipse-temurin:11-jdk AS build_image
RUN apt update && apt install -y maven git
RUN git clone https://github.com/devopshydclub/vprofile-project.git
RUN cd vprofile-project && git checkout docker && mvn install

# Run stage
FROM tomcat:9.0.83-jdk11-temurin
RUN rm -rf /usr/local/tomcat/webapps/*
COPY --from=build_image vprofile-project/target/vprofile-v2.war /usr/local/tomcat/webapps/ROOT.war
EXPOSE 8080
CMD ["catalina.sh", "run"]
