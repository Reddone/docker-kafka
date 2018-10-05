FROM reddone/jre-zulu:1.0@sha256:2d2bbfd8af439e92a1f61af65fb358821da41d91cd72a12563e1634388796fe3

ENV CP_ARCHIVE_VERSION=v4.0.2 \
    CP_PACKAGE_VERSION=4.0.2 \
    MAVEN_VERSION=3.5.2 \
    MAVEN_FLAGS="-Dmaven.test.skip=true -Dconfluent.maven.repo=http://packages.confluent.io/maven/"
# Default versions are found in the corresponding branch of: https://github.com/confluentinc/common/blob/master/pom.xml

WORKDIR /usr/local

RUN set -ex; \
  WORKDIR=$PWD; \
  mkdir -p $WORKDIR/share/java; \
  \
  export DEBIAN_FRONTEND=noninteractive; \
  runDeps=''; \
  buildDeps='curl ca-certificates xmlstarlet'; \
  apt-get update && apt-get install -y $runDeps $buildDeps --no-install-recommends; \
  \
  PATH=$PATH:/opt/maven/bin; \
  mkdir -p /opt/maven; \
  curl -SLs https://archive.apache.org/dist/maven/maven-3/$MAVEN_VERSION/binaries/apache-maven-$MAVEN_VERSION-bin.tar.gz | tar -xzf - --strip-components=1 -C /opt/maven; \
  mvn --version; \
  \
  mkdir -p /opt/src/common; cd /opt/src/common; \
  curl -SLs https://github.com/confluentinc/common/archive/$CP_ARCHIVE_VERSION.tar.gz | tar -xzf - --strip-components=1 -C ./; \
  mvn $MAVEN_FLAGS install; \
  \
  mkdir -p /opt/src/rest-utils; cd /opt/src/rest-utils; \
  curl -SLs https://github.com/confluentinc/rest-utils/archive/$CP_ARCHIVE_VERSION.tar.gz | tar -xzf - --strip-components=1 -C ./; \
  mv pom.xml pom.xml.old; \
  xmlstarlet ed -N p=http://maven.apache.org/POM/4.0.0 -u "/p:project/p:parent/p:artifactId" -v 'common' pom.xml.old > pom.xml; \
  mvn $MAVEN_FLAGS install; \
  \
  mkdir -p /opt/src/schema-registry; cd /opt/src/schema-registry; \
  curl -SLs https://github.com/confluentinc/schema-registry/archive/$CP_ARCHIVE_VERSION.tar.gz | tar -xzf - --strip-components=1 -C ./; \
  mvn $MAVEN_FLAGS install; \
  \
  mkdir -p /opt/src/kafka-rest; cd /opt/src/kafka-rest; \
  curl -SLs https://github.com/confluentinc/kafka-rest/archive/$CP_ARCHIVE_VERSION.tar.gz | tar -xzf - --strip-components=1 -C ./; \
  mvn $MAVEN_FLAGS install; \
  \
  cd $WORKDIR; \
  \
  mv /opt/src/common/package/target/common-package-$CP_PACKAGE_VERSION-package/share/java/confluent-common ./share/java/; \
  mv /opt/src/rest-utils/package/target/rest-utils-package-$CP_PACKAGE_VERSION-package/share/java/rest-utils ./share/java/; \
  \
  mv /opt/src/schema-registry/package-schema-registry/target/kafka-schema-registry-package-$CP_PACKAGE_VERSION-package/bin/* ./bin/; \
  mv /opt/src/schema-registry/package-schema-registry/target/kafka-schema-registry-package-$CP_PACKAGE_VERSION-package/share/java/* ./share/java/; \
  mv /opt/src/schema-registry/package-schema-registry/target/kafka-schema-registry-package-$CP_PACKAGE_VERSION-package/etc/* /etc/; \
  \
  mv /opt/src/kafka-rest/target/kafka-rest-$CP_PACKAGE_VERSION-package/bin/* ./bin/; \
  mv /opt/src/kafka-rest/target/kafka-rest-$CP_PACKAGE_VERSION-package/share/java/* ./share/java/; \
  mv /opt/src/kafka-rest/target/kafka-rest-$CP_PACKAGE_VERSION-package/etc/* /etc; \
  \
  rm -Rf /opt/src /opt/maven /root/.m2; \
  \
  apt-get purge -y --auto-remove $buildDeps; \
  rm -rf /var/lib/apt/lists/*; \
  rm -rf /var/log/dpkg.log /var/log/alternatives.log /var/log/apt

COPY docker-help.sh /usr/local/bin/docker-help
ENTRYPOINT ["docker-help"]
