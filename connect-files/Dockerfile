FROM reddone/kafka@sha256:1d74f0a05694e68262082f6098ae093dc65f60a6d7d60e8a5d9659c675289557

COPY worker.properties ./config/
COPY connect-files.sh ./bin/

ENV FILES_LIST_CMD="find /logs/ -name *.log"

# Set up some sample logs
RUN mkdir /logs/; \
  echo "Mount /logs and/or change FILES_LIST_CMD (currently '$FILES_LIST_CMD') to read real content instead" > /logs/samplefile1.log;

ENTRYPOINT ["./bin/connect-files.sh"]
