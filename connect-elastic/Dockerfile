FROM reddone/kafka:1.0@sha256:20ed8677c465a749c4d97a023204111833fa3c5ce66021647331d9493f4f8ec2

# referenced from dependency versions in the maven pom
ENV CP_VERSION=4.0.2 \
    MAVEN_VERSION=3.5.2

ADD pom.xml /tmp/pom.xml

# avoid patching kafka-run-class.sh, by hijacking one of the unused paths where it globs for jars
ENV JARS_DIR=/opt/kafka/core/build/dependant-libs-${SCALA_VERSION}

RUN set -ex; \
  export DEBIAN_FRONTEND=noninteractive; \
  runDeps=''; \
  buildDeps='curl ca-certificates'; \
  apt-get update && apt-get install -y $runDeps $buildDeps --no-install-recommends; \
  \
  PATH=$PATH:/opt/maven/bin; \
  mkdir -p /opt/maven; \
  curl -SLs https://archive.apache.org/dist/maven/maven-3/$MAVEN_VERSION/binaries/apache-maven-$MAVEN_VERSION-bin.tar.gz | tar -xzf - --strip-components=1 -C /opt/maven; \
  mvn --version; \
  \
  mvn -f /tmp/pom.xml dependency:copy-dependencies -DincludeScope=runtime -DoutputDirectory=$JARS_DIR; \
  \
  rm -Rf /opt/src /opt/maven /root/.m2; \
  \
  apt-get purge -y --auto-remove $buildDeps; \
  rm -rf /var/lib/apt/lists/*; \
  rm -rf /var/log/dpkg.log /var/log/alternatives.log /var/log/apt

ENTRYPOINT ["./bin/connect-distributed.sh"]