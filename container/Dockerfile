FROM registry.access.redhat.com/ubi7/ubi:latest
LABEL maintainer "Chen Gao <Chen.Gao@.ibm.com>"

ENV IOT_REPO_PATH=/root/db2eventstore-IoT-Analytics \
    KAFKA_REPO_PATH=/root/db2eventstore-kafka
ARG SETUP_AREA=${IOT_REPO_PATH}/container/setup
ARG BRANCH=master

# Install neccessary packages
RUN yum install -y \
    wget \
    vim \
    unzip \
    less \
    git \
    gcc \
    make \
    sudo \
    zlib-devel \
    openssl-devel \
    && yum clean all \
    && rm -rf /var/cache/yum

RUN git clone --single-branch --branch ${BRANCH} https://github.com/IBMProjectEventStore/db2eventstore-IoT-Analytics.git ${IOT_REPO_PATH}

# Install jq
RUN wget -O /usr/local/bin/jq https://github.com/stedolan/jq/releases/download/jq-1.5/jq-linux64
RUN chmod +x /usr/local/bin/jq

# Install screen
RUN wget -P /tmp/ http://mirror.centos.org/centos/7/os/x86_64/Packages/screen-4.1.0-0.25.20120314git3c2946.el7.x86_64.rpm
RUN yum localinstall -y /tmp/screen-4.1.0-0.25.20120314git3c2946.el7.x86_64.rpm && yum clean all && rm -rf /var/cache/yum && rm -rf /tmp/screen-4.1.0-0.25.20120314git3c2946.el7.x86_64.rpm

# Setup Screen
RUN echo -e 'startup_message off \nhardstatus on \nhardstatus alwayslastline \nvbell off \nhardstatus string "%{.bW}%-w%{..G}%n %t%{-}%+w %=%{..G} %H %{..Y} %m/%d %C%a"' > ${HOME}/.screenrc

# Setup vim 
RUN echo -e 'syntax enable \nset tabstop=4 \nset number \nset hlsearch \nset incsearch' > ${HOME}/.vimrc

# Setup spark/scala/python/rest
ENV SPARK_HOME="/spark_home"
ENV PATH=$PATH:$SPARK_HOME/bin:$SPARK_HOME/sbin
RUN ${SETUP_AREA}/setup-java.sh
RUN ${SETUP_AREA}/setup-spark.sh
RUN ${SETUP_AREA}/setup-scala.sh
RUN ${SETUP_AREA}/setup-python.sh
RUN ${SETUP_AREA}/setup-kafka.sh
RUN ${IOT_REPO_PATH}/rest/install.sh

# Copy ES2.0 client libraries
RUN echo "Downloading Event Store 2.0 client libraries, it will take some time..."
RUN wget -P ${SPARK_HOME}/jars/ https://repo1.maven.org/maven2/com/ibm/event/ibm-db2-eventstore-client-spark-2.2.1/2.0.0/ibm-db2-eventstore-client-spark-2.2.1-2.0.0.jar

# Create client config file
RUN mkdir -p /bluspark/external_conf/
RUN touch /bluspark/external_conf/bluspark.conf
WORKDIR /root
