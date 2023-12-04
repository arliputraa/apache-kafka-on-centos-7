# Installation Apache Kafka On CentOS 7

## Download Kafka
    wget https://downloads.apache.org/kafka/3.4.1/kafka_2.13-3.4.1.tgz -O /tmp/kafka_2.13-3.4.1.tgz


## Extract Kafka
    tar -xzf kafka_2.13-3.4.1.tgz

### Rename and Move Kafka 
    sudo mv kafka_2.13-3.4.1 /opt/kafka

## Create a systemd service unit for Apache Zookeeper
    cd /opt/kafka/
    vi /etc/systemd/system/zookeeper.service
append this:

    [Unit]
    Description=Apache Zookeeper server
    Documentation=http://zookeeper.apache.org
    Requires=network.target remote-fs.target
    After=network.target remote-fs.target
    
    [Service]
    Type=simple
    User=root
    ExecStart=/opt/kafka/bin/zookeeper-server-start.sh /opt/kafka/config/zookeeper.properties
    ExecStop=/opt/kafka/bin/zookeeper-server-stop.sh
    Restart=on-abnormal
    
    [Install]
    WantedBy=multi-user.target

## Create a systemd service unit for Apache Kafka
    vi /etc/systemd/system/kafka.service
append this:

    [Unit]
    Description=Apache Kafka Server
    Documentation=http://kafka.apache.org/documentation.html
    Requires=zookeeper.service
    After=zookeeper.service
    
    [Service]
    Type=simple
    User=root
    ExecStart=/bin/sh -c '/opt/kafka/bin/kafka-server-start.sh /opt/kafka/config/server.properties > /opt/kafka/logs/kafka.log 2>&1'
    ExecStop=/opt/kafka/bin/kafka-server-stop.sh
    Restart=on-abnormal
    
    [Install]
    WantedBy=multi-user.target

## Edit server.properties of Kafka
    vi /opt/kafka/config/server.properties

change the logs dir to:

    log.dirs=/opt/kafka/logs

append this to EOF:
    
    delete.topic.enable = true

## Restart server or Reboot
    Sudo reboot now

## Enable and start Apache Zookeeper and Kafka services
    systemctl daemon-reload
    systemctl enable --now zookeeper.service
    systemctl enable --now kafka.service

## Verify the status of Apache Kafka Service
    systemctl status zookeeper.service 
    systemctl status kafka.service

## Create a Topic 
    /opt/kafka/bin/kafka-topics.sh --create --topic HidayatTopic --bootstrap-server localhost:9092
 
![image](https://github.com/arliputraa/apache-kafka-on-centos-7/assets/110078907/6c76da88-07aa-4d16-92cb-5f6b8c2ee154)

## Show Topic Details 
    /opt/kafka/bin/kafka-topics.sh --describe --topic HidayatTopic --bootstrap-server localhost:9092

![image](https://github.com/arliputraa/apache-kafka-on-centos-7/assets/110078907/eb0624bf-6e3c-4c2d-a4a2-36e0f0d16d8f)

## WRITE SOME EVENTS INTO THE TOPIC
    /opt/kafka/bin/kafka-console-producer.sh --topic HidayatTopic --bootstrap-server localhost:9092

![image](https://github.com/arliputraa/apache-kafka-on-centos-7/assets/110078907/7c8d6d8c-4941-4592-9013-77a4d070ecf0)
to end ctrl+c

## READ EVENT FROM TOPIC
    /opt/kafka/bin/kafka-console-consumer.sh --topic HidayatTopic --from-beginning --bootstrap-server localhost:9092

![image](https://github.com/arliputraa/apache-kafka-on-centos-7/assets/110078907/de38cbe5-9707-46aa-972a-2a3f469aa564)

