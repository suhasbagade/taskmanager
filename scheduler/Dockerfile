FROM alpine AS builder

FROM ofayau/ejre:8-jre

COPY target/scheduler-0.1-SNAPSHOT.jar scheduler.jar
COPY target/services-0.1-SNAPSHOT.jar services.jar

EXPOSE 9090

CMD ["java","-jar","scheduler.jar"]
