FROM openjdk:8 AS BUILD_IMAGE
RUN apt update && apt install maven -y
RUN git clone -b main https://github.com/Bedier1/vprofile.git
                          
RUN cd vprofile && mvn install

FROM tomcat:8.5.84-jre11
RUN rm -rf /usr/local/tomcat/webapps/*
COPY --from=BUILD_IMAGE vprofile/target/vprofile-v*.war /usr/local/tomcat/webapps/ROOT.war
EXPOSE 8080
CMD ["catalina.sh", "run"]
#WORKDIR /usr/local/tomcat/webapps/
#VOLUME [ "/usr/local/tomcat/webapps" ]
