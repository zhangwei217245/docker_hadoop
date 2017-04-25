FROM zhangwei217245/docker_java_8:1.8.0_121
MAINTAINER zhangwei217245

ENV HADOOP_VERSION 2.8.0

# Download hadoop tarball and install to /usr/local
RUN curl -s http://apache.cs.utah.edu/hadoop/common/hadoop-$HADOOP_VERSION/hadoop-$HADOOP_VERSION.tar.gz | tar -zx -C /usr/local/
RUN cd /usr/local && ln -s ./hadoop-$HADOOP_VERSION hadoop

# Installing google snappy compression library for HDFS
RUN apt install -y libsnappy-java libsnappy-dev libsnappy-jni
COPY libsnappy/* /usr/local/lib/

ENV LD_LIBRARY_PATH /usr/local/lib

ENV HADOOP_PREFIX /usr/local/hadoop
ENV HADOOP_COMMON_HOME /usr/local/hadoop
ENV HADOOP_HDFS_HOME /usr/local/hadoop
ENV HADOOP_MAPRED_HOME /usr/local/hadoop
ENV HADOOP_YARN_HOME /usr/local/hadoop
ENV HADOOP_CONF_DIR /usr/local/hadoop/etc/hadoop
ENV YARN_CONF_DIR $HADOOP_PREFIX/etc/hadoop

RUN sed -i '/^export JAVA_HOME/ s:.*:export JAVA_HOME=/usr/lib/jvm/java-8-oracle\nexport HADOOP_PREFIX=/usr/local/hadoop\nexport HADOOP_HOME=/usr/local/hadoop\n:' $HADOOP_PREFIX/etc/hadoop/hadoop-env.sh
RUN sed -i '/^export HADOOP_CONF_DIR/ s:.*:export HADOOP_CONF_DIR=/usr/local/hadoop/etc/hadoop/:' $HADOOP_PREFIX/etc/hadoop/hadoop-env.sh

RUN mkdir $HADOOP_PREFIX/input
RUN cp $HADOOP_PREFIX/etc/hadoop/*.xml $HADOOP_PREFIX/input


