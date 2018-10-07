FROM reddone/kafka@sha256:adf0ca127dfd77ec527707f3ecc2feefad8f437f03a23fb87a14509366963cc3

COPY worker.properties ./config/
COPY connect-files.sh ./bin/

ENV FILES_LIST_CMD="find /logs/ -name *.log"

# Set up some sample logs
RUN mkdir /logs/; \
  echo "Mount /logs and/or change FILES_LIST_CMD (currently '$FILES_LIST_CMD') to read real content instead" > /logs/samplefile1.log;

ENTRYPOINT ["./bin/connect-files.sh"]
