FROM zhangwei217245/docker_java_8:1.8.0_121
MAINTAINER zhangwei217245 <zhangwei217245@gmail.com>

ENV HADOOP_VERSION 2.8.0

# Download hadoop tarball and install to /usr/local
RUN curl -s http://apache.cs.utah.edu/hadoop/common/hadoop-$HADOOP_VERSION/hadoop-$HADOOP_VERSION.tar.gz | tar -zx -C /usr/local/
WORKDIR /usr/local
RUN ln -s ./hadoop-$HADOOP_VERSION hadoop

# Installing google snappy compression library for HDFS
RUN apt install -y libsnappy-java libsnappy-dev libsnappy-jni
COPY libsnappy/* /usr/local/lib/

ENV LD_LIBRARY_PATH /usr/local/lib
WORKDIR /usr/local/lib/
RUN tar zxvf hadoop-snappy-0.0.1-SNAPSHOT.tar.gz 
WORKDIR /usr/local/lib/hadoop-snappy-0.0.1-SNAPSHOT/lib/
RUN cp -r * $HADOOP_PREFIX/lib/

# set up environment variables

ENV HADOOP_PREFIX /usr/local/hadoop
ENV HADOOP_COMMON_HOME /usr/local/hadoop
ENV HADOOP_HDFS_HOME /usr/local/hadoop
ENV HADOOP_MAPRED_HOME /usr/local/hadoop
ENV HADOOP_YARN_HOME /usr/local/hadoop
ENV HADOOP_CONF_DIR /usr/local/hadoop/etc/hadoop
ENV YARN_CONF_DIR $HADOOP_PREFIX/etc/hadoop

COPY ./conf/core-site.xml $HADOOP_CONF_DIR/core-site.xml
COPY ./conf/hdfs-site.xml $HADOOP_CONF_DIR/hdfs-site.xml
COPY ./conf/mapred-site.xml $HADOOP_CONF_DIR/mapred-site.xml
COPY ./conf/yarn-site.xml $HADOOP_CONF_DIR/yarn-site.xml
COPY ./conf/hadoop-env.sh $HADOOP_CONF_DIR/hadoop-env.sh


# set up hadoop-env.sh
RUN sed -i '/^export JAVA_HOME/ s:.*:export JAVA_HOME=/usr/lib/jvm/java-8-oracle\nexport HADOOP_PREFIX=/usr/local/hadoop\nexport HADOOP_HOME=/usr/local/hadoop\n:' $HADOOP_PREFIX/etc/hadoop/hadoop-env.sh
RUN sed -i '/^export HADOOP_CONF_DIR/ s:.*:export HADOOP_CONF_DIR=/usr/local/hadoop/etc/hadoop/:' $HADOOP_PREFIX/etc/hadoop/hadoop-env.sh

# Not quite sure what is the use of this. 
RUN mkdir $HADOOP_PREFIX/input
RUN cp $HADOOP_PREFIX/etc/hadoop/*.xml $HADOOP_PREFIX/input

ADD ./conf/ssh_config /root/.ssh/config
RUN chmod 600 /root/.ssh/config
RUN chown root:root /root/.ssh/config

# workingaround docker.io build error
RUN ls -la /usr/local/hadoop/etc/hadoop/*-env.sh
RUN chmod +x /usr/local/hadoop/etc/hadoop/*-env.sh
RUN ls -la /usr/local/hadoop/etc/hadoop/*-env.sh

# fix the 254 error code
RUN sed  -i "/^[^#]*UsePAM/ s/.*/#&/"  /etc/ssh/sshd_config
RUN echo "UsePAM no" >> /etc/ssh/sshd_config
RUN echo "Port 2122" >> /etc/ssh/sshd_config

RUN $HADOOP_PREFIX/bin/hdfs namenode -format

# by default, hadoop will put all its data files in to /tmp. So we expose /tmp to host machine.
# these files include namenode files, datanode files and secondary namemode files. 
VOLUME /tmp $HADOOP_PREFIX/logs

# Hdfs ports
EXPOSE 50010 50020 50070 50071 50075 50090 50100 50105 50470 50475 2049 4242 8020 9000
# Mapred ports
EXPOSE 10020 13562 19888 19890
#Yarn ports
EXPOSE 8030 8031 8032 8033 8040 8042 8044 8045 8046 8047 8048 8088 8090 8188 8190 8788 10200
#Other ports
EXPOSE 49707 2122
