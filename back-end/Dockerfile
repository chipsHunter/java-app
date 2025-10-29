#------build stage------------#
FROM gradle:8.5.0-jdk21-jammy@sha256:873def2b8a73d00f5616043ac9ff65576b055942701391ad66499f1a1f53f1b2 AS builder

WORKDIR /app
COPY . .

RUN gradle bootJar

#------final stage------------#
FROM alpine/java:21-jre@sha256:a0433ecc16a0a9e389e216753019cc97f69f0973aecfadaf357d31838078bab5


WORKDIR /app

EXPOSE 8081
COPY --from=builder  /app/build/libs/ci-back-end-0.0.1-SNAPSHOT.jar /app/app.jar

USER appuser

CMD ["java", "-jar", "/app/app.jar"]