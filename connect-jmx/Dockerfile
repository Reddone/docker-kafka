FROM reddone/kafka:1.0@sha256:2ba46c7461668541a66d3eea366c0986d9c97c6ff2d2743c2c0c21c4da531e2b

ENV SRIJITHS_KAFKA_CONNECTORS_COMMIT=111d4f64cba027df7d2a9b3b750df53dcd50c705

RUN set -ex; \
  buildDeps='curl ca-certificates'; \
  apt-get update && apt-get install -y $buildDeps --no-install-recommends; \
  \
  MAVEN_VERSION=3.5.0 PATH=$PATH:$(pwd)/maven/bin; \
  mkdir ./maven; \
  curl -SLs https://archive.apache.org/dist/maven/maven-3/$MAVEN_VERSION/binaries/apache-maven-$MAVEN_VERSION-bin.tar.gz | tar -xzf - --strip-components=1 -C ./maven; \
  mvn --version; \
  \
  mkdir ./kafka-connectors; \
  cd ./kafka-connectors; \
  curl -SLs https://github.com/srijiths/kafka-connectors/archive/$SRIJITHS_KAFKA_CONNECTORS_COMMIT.tar.gz \
    | tar -xzf - --strip-components=1 -C ./; \
  mvn clean install; \
  cd ..; \
  mv ~/.m2/repository/com/sree/kafka/kafka-connect-jmx/0.0.1/kafka-connect-jmx-0.0.1-jar-with-dependencies.jar ./libs/; \
  rm -rf ./kafka-connectors; \
  rm -rf ./maven ~/.m2; \
  \
  apt-get purge -y --auto-remove $buildDeps; \
  rm -rf /var/lib/apt/lists/*; \
  rm /var/log/dpkg.log /var/log/apt/*.log

COPY *.properties ./config/

ENTRYPOINT ["./bin/connect-standalone.sh"]

CMD ["./config/worker.properties", "./config/connect-jmx.properties"]
