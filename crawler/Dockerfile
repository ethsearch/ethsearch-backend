FROM debian:stretch

WORKDIR /root/


# Install dependencies
RUN apt-get update
RUN apt-get install -y openjdk-8-jdk-headless curl vim patch

# Set up JAVA_HOME
RUN echo 'export JAVA_HOME=/usr/lib/jvm/java-8-openjdk-amd64' >> $HOME/.bashrc

# Download nutch
RUN curl -o apache-nutch-1.16-bin.tar.gz http://apache.claz.org/nutch/1.16/apache-nutch-1.16-bin.tar.gz \
    && curl -o apache-nutch-1.16-src.tar.gz http://apache.claz.org/nutch/1.16/apache-nutch-1.16-src.tar.gz

# Dummy arg to force build from this step
ARG CRAWL_VER=unknown

# Extract nutch
RUN tar xvzf apache-nutch-1.16-bin.tar.gz \
    && mkdir -p apache-nutch-1.16/src \
    && tar xvzf apache-nutch-1.16-src.tar.gz -C apache-nutch-1.16/src

# Download nutch.patch from repo
RUN curl -o nutch.patch https://raw.githubusercontent.com/ethsearch/ethsearch-backend/master/crawler/nutch.patch 

# Apply nutch.patch 
RUN cd apache-nutch-1.16 && patch -p1 <../nutch.patch 

CMD /bin/bash -c "source $HOME/.bashrc && cd apache-nutch-1.16 && bin/crawl -i -s urls/seed.txt crawl 3 && curl 'http://ethsear.ch:8080/solr/nutch/update' -H \"Content-type: text/xml\" --data-binary '<delete><query>host:medium.com AND url:*source</query></delete>' && curl 'http://ethsear.ch:8080/solr/nutch/update' -H \"Content-type: text/xml\" --data-binary '<commit />'"

