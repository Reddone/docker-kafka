FROM reddone/kafka:1.0@sha256:2ba46c7461668541a66d3eea366c0986d9c97c6ff2d2743c2c0c21c4da531e2b

COPY worker.properties ./config/
COPY connect-files.sh ./bin/

ENV FILES_LIST_CMD="find /logs/ -name *.log"

# Set up some sample logs
RUN mkdir /logs/; \
  echo "Mount /logs and/or change FILES_LIST_CMD (currently '$FILES_LIST_CMD') to read real content instead" > /logs/samplefile1.log;

ENTRYPOINT ["./bin/connect-files.sh"]
