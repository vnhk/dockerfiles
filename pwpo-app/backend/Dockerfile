ARG PWPO_SERVICE_VERSION='1.0'
ARG IE_ENTITIES_VERSION='1.0.0'
ARG HISTORY_TABLES_CORE_VERSION='1.0.0'

FROM maven:3.9.2 AS builder

WORKDIR /app

COPY bervan-utils ./bervan-utils

RUN mvn -f='./bervan-utils/history-tables-core' install -DskipTests
RUN mvn -f='./bervan-utils/ie-entities' install -DskipTests

ARG HISTORY_TABLES_CORE_VERSION

RUN mvn install:install-file -Dfile=./bervan-utils/history-tables-core/target/history-tables-core-${HISTORY_TABLES_CORE_VERSION}.jar -Dpackaging=jar -DgroupId=com.bervan -DartifactId=history-tables-core -Dversion=${HISTORY_TABLES_CORE_VERSION}

ARG IE_ENTITIES_VERSION

RUN mvn install:install-file -Dfile=./bervan-utils/ie-entities/target/ie-entities-${IE_ENTITIES_VERSION}.jar -DgroupId=com.bervan -DartifactId=ie-entities -Dversion=${IE_ENTITIES_VERSION} -Dpackaging=jar

COPY pwpo-service ./pwpo-service

RUN mvn -f='./pwpo-service' install -DskipTests

FROM openjdk:18-oracle AS runtime

WORKDIR /app

ARG PWPO_SERVICE_VERSION

COPY --from=builder /app/pwpo-service/target/pwpo-service-${PWPO_SERVICE_VERSION}.jar ./pwpo-service.jar
EXPOSE 8080

CMD ["java", "-jar", "-Dspring.profiles.active=production", "pwpo-service.jar"]