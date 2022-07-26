FROM maven:3.8.6-openjdk-18-slim

WORKDIR /app

COPY . .

RUN mvn package -DskipTests && \
    mv target/worker-jar-with-dependencies.jar /run/worker.jar

CMD java -jar /run/worker.jar