ARG APP_SERVICE_VERSION='1.0-SNAPSHOT'
ARG IE_ENTITIES_VERSION='2.0.2'
ARG HISTORY_TABLES_CORE_VERSION='2.0.0'
ARG CORE_VERSION='2.0.0'

FROM maven:3.8.5-openjdk-17 AS builder

WORKDIR /app

# Build Bervan Utils
COPY code/bervan-utils ./bervan-utils

# RUN rm -rf ~/.m2/repository/

RUN mvn -f='./bervan-utils/core' install -DskipTests
RUN mvn -f='./bervan-utils/history-tables-core' install -DskipTests
RUN mvn -f='./bervan-utils/ie-entities' install -DskipTests

ARG CORE_VERSION

RUN mvn install:install-file -Dfile=./bervan-utils/core/target/core-${CORE_VERSION}.jar -Dpackaging=jar -DgroupId=com.bervan -DartifactId=core -Dversion=${CORE_VERSION}

ARG HISTORY_TABLES_CORE_VERSION

RUN mvn install:install-file -Dfile=./bervan-utils/history-tables-core/target/history-tables-core-${HISTORY_TABLES_CORE_VERSION}.jar -Dpackaging=jar -DgroupId=com.bervan -DartifactId=history-tables-core -Dversion=${HISTORY_TABLES_CORE_VERSION}

ARG IE_ENTITIES_VERSION

RUN mvn install:install-file -Dfile=./bervan-utils/ie-entities/target/ie-entities-${IE_ENTITIES_VERSION}.jar -DgroupId=com.bervan -DartifactId=ie-entities -Dversion=${IE_ENTITIES_VERSION} -Dpackaging=jar

# Copy pom.xml files first and download dependencies
COPY code/my-tools/common-vaadin/pom.xml ./my-tools/common-vaadin/pom.xml
COPY code/my-tools/shopping-stats-server-app/pom.xml ./my-tools/shopping-stats-server-app/pom.xml
COPY code/my-tools/file-storage-app/pom.xml ./my-tools/file-storage-app/pom.xml
COPY code/my-tools/english-text-stats-app/pom.xml ./my-tools/english-text-stats-app/pom.xml
COPY code/my-tools/learning-language-app/pom.xml ./my-tools/learning-language-app/pom.xml
COPY code/my-tools/pocket-app/pom.xml ./my-tools/pocket-app/pom.xml
COPY code/my-tools/spreadsheet-app/pom.xml ./my-tools/spreadsheet-app/pom.xml
COPY code/my-tools/interview-app/pom.xml ./my-tools/interview-app/pom.xml
COPY code/my-tools/project-mgmt-app/pom.xml ./my-tools/project-mgmt-app/pom.xml
COPY code/my-tools/canvas-app/pom.xml ./my-tools/canvas-app/pom.xml
COPY code/my-tools/streaming-platform-app/pom.xml ./my-tools/streaming-platform-app/pom.xml
COPY code/my-tools/my-tools-vaadin-app/pom.xml ./my-tools/my-tools-vaadin-app/pom.xml

RUN --mount=type=cache,target=/root/.m2 mvn -f='./my-tools/common-vaadin' dependency:go-offline
RUN --mount=type=cache,target=/root/.m2 mvn -f='./my-tools/shopping-stats-server-app' dependency:go-offline
RUN --mount=type=cache,target=/root/.m2 mvn -f='./my-tools/file-storage-app' dependency:go-offline
RUN --mount=type=cache,target=/root/.m2 mvn -f='./my-tools/english-text-stats-app' dependency:go-offline
RUN --mount=type=cache,target=/root/.m2 mvn -f='./my-tools/learning-language-app' dependency:go-offline
RUN --mount=type=cache,target=/root/.m2 mvn -f='./my-tools/pocket-app' dependency:go-offline
RUN --mount=type=cache,target=/root/.m2 mvn -f='./my-tools/spreadsheet-app' dependency:go-offline
RUN --mount=type=cache,target=/root/.m2 mvn -f='./my-tools/interview-app' dependency:go-offline
RUN --mount=type=cache,target=/root/.m2 mvn -f='./my-tools/project-mgmt-app' dependency:go-offline
RUN --mount=type=cache,target=/root/.m2 mvn -f='./my-tools/canvas-app' dependency:go-offline
RUN --mount=type=cache,target=/root/.m2 mvn -f='./my-tools/streaming-platform-app' dependency:go-offline
RUN --mount=type=cache,target=/root/.m2 mvn -f='./my-tools/my-tools-vaadin-app' dependency:go-offline

# Copy source code and build
COPY code/my-tools ./my-tools

COPY code/config/configuration.json ./configuration/configuration.json

RUN --mount=type=cache,target=/root/.m2 mvn -f='./my-tools/common-vaadin' clean install -DskipTests -U
RUN --mount=type=cache,target=/root/.m2 mvn -f='./my-tools/shopping-stats-server-app' clean install -DskipTests -U
RUN --mount=type=cache,target=/root/.m2 mvn -f='./my-tools/file-storage-app' clean install -DskipTests -U
RUN --mount=type=cache,target=/root/.m2 mvn -f='./my-tools/english-text-stats-app' clean install -DskipTests -U
RUN --mount=type=cache,target=/root/.m2 mvn -f='./my-tools/learning-language-app' clean install -DskipTests -U
RUN --mount=type=cache,target=/root/.m2 mvn -f='./my-tools/pocket-app' clean install -DskipTests -U
RUN --mount=type=cache,target=/root/.m2 mvn -f='./my-tools/spreadsheet-app' clean install -DskipTests -U
RUN --mount=type=cache,target=/root/.m2 mvn -f='./my-tools/interview-app' clean install -DskipTests -U
RUN --mount=type=cache,target=/root/.m2 mvn -f='./my-tools/project-mgmt-app' clean install -DskipTests -U
RUN --mount=type=cache,target=/root/.m2 mvn -f='./my-tools/canvas-app' clean install -DskipTests -U
RUN --mount=type=cache,target=/root/.m2 mvn -f='./my-tools/streaming-platform-app' clean install -DskipTests -U
RUN --mount=type=cache,target=/root/.m2 mvn -f='./my-tools/my-tools-vaadin-app' clean -Pproduction install -DskipTests -U

FROM openjdk:17 AS runtime

WORKDIR /app

ARG APP_SERVICE_VERSION

COPY --from=builder /app/my-tools/my-tools-vaadin-app/target/my-tools-vaadin-app-${APP_SERVICE_VERSION}.jar ./my-tools-vaadin-app.jar
COPY --from=builder /app/configuration/configuration.json ./configuration/configuration.json

CMD ["java", "-jar", "-Dspring.profiles.active=production", "my-tools-vaadin-app.jar"]