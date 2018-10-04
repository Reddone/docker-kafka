FROM solsson/kafka-jre@sha256:06dabfc8cacd0687c8f52c52afd650444fb6d4a8e0b85f68557e6e7a5c71667c

ENV KAFKA_MONITOR_REPO=https://github.com/linkedin/kafka-monitor \
    KAFKA_MONITOR_VERSION=1.0.0 \
    KAFKA_MONITOR_SHA256=360d0d63f44d45273bf668e79d679228e87372be84a00e7a131759e88537887c

RUN set -ex; \
  export DEBIAN_FRONTEND=noninteractive; \
  runDeps=''; \
  buildDeps='curl ca-certificates unzip'; \
  apt-get update && apt-get install -y $runDeps $buildDeps --no-install-recommends; \
  \
  cd /opt; \
  GRADLE_VERSION=4.3.1 PATH=$PATH:$(pwd)/gradle-$GRADLE_VERSION/bin; \
  curl -SLs -o gradle-$GRADLE_VERSION-bin.zip https://services.gradle.org/distributions/gradle-$GRADLE_VERSION-bin.zip; \
  echo "15ebe098ce0392a2d06d252bff24143cc88c4e963346582c8d88814758d93ac7 gradle-$GRADLE_VERSION-bin.zip" | sha256sum -c -; \
  unzip gradle-$GRADLE_VERSION-bin.zip; \
  rm gradle-$GRADLE_VERSION-bin.zip; \
  gradle -v; \
  \
  mkdir -p /opt/kafka-monitor; \
  curl -o monitor.tar.gz -SLs "$KAFKA_MONITOR_REPO/archive/$KAFKA_MONITOR_VERSION.tar.gz"; \
  echo "$KAFKA_MONITOR_SHA256 monitor.tar.gz" | sha256sum -c; \
  tar -xzf monitor.tar.gz --strip-components=1 -C /opt/kafka-monitor; \
  rm monitor.tar.gz; \
  \
  cd /opt/kafka-monitor; \
  ./gradlew jar; \
  \
  sed -i 's/localhost:2181/zookeeper:2181/' config/kafka-monitor.properties; \
  sed -i 's/localhost:9092/bootstrap:9092/' config/kafka-monitor.properties; \
  \
  cat config/kafka-monitor.properties; \
  cat config/log4j.properties; \
  \
  rm -rf /opt/gradle* /root/.gradle; \
  \
  apt-get purge -y --auto-remove $buildDeps nodejs; \
  rm -rf /var/lib/apt/lists/*; \
  rm -rf /var/log/dpkg.log /var/log/alternatives.log /var/log/apt

WORKDIR /opt/kafka-monitor

ENTRYPOINT ["./bin/kafka-monitor-start.sh"]
CMD ["/opt/kafka-monitor/config/kafka-monitor.properties"]
