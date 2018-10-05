FROM reddone/kafka:1.0@sha256:20ed8677c465a749c4d97a023204111833fa3c5ce66021647331d9493f4f8ec2

COPY worker.properties ./config/
COPY connect-files.sh ./bin/

ENV FILES_LIST_CMD="find /logs/ -name *.log"

# Set up some sample logs
RUN mkdir /logs/; \
  echo "Mount /logs and/or change FILES_LIST_CMD (currently '$FILES_LIST_CMD') to read real content instead" > /logs/samplefile1.log;

ENTRYPOINT ["./bin/connect-files.sh"]
