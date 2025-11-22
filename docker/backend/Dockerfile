# 使用多阶段构建后端服务镜像
FROM maven:3.9.9-eclipse-temurin-8 AS build
WORKDIR /workspace
COPY jshERP-boot/pom.xml jshERP-boot/pom.xml
COPY jshERP-boot/src jshERP-boot/src
COPY jshERP-boot/docs jshERP-boot/docs
RUN --mount=type=cache,target=/root/.m2 \
    mvn -B -f jshERP-boot/pom.xml -DskipTests=true clean package

FROM eclipse-temurin:8-jre-alpine
ENV SPRING_PROFILES_ACTIVE=prod \
    JAVA_OPTS="-XX:MaxRAMPercentage=75 -XX:+UseG1GC -Djava.security.egd=file:/dev/./urandom"
WORKDIR /opt/app
COPY --from=build /workspace/jshERP-boot/target/jshERP.jar app.jar
RUN apk add --no-cache curl \
    && addgroup -S jsherp \
    && adduser -S -G jsherp -h /opt/app jsherp \
    && mkdir -p /opt/jshERP/upload /opt/tmp/tomcat plugins pluginConfig \
    && chown -R jsherp:jsherp /opt/app /opt/jshERP /opt/tmp
USER jsherp
VOLUME ["/opt/jshERP/upload","/opt/tmp/tomcat","/opt/app/plugins","/opt/app/pluginConfig"]
EXPOSE 9999
ENTRYPOINT ["sh","-c","exec java $JAVA_OPTS -jar app.jar"]
