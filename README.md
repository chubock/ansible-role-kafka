Role Name
=========

A role to install `Apache Kafka` cluster.

Requirements
------------

`Apache Kafka` requires `Java`. If java is installed on the host, specify it's location with `java_home` variable. Otherwise, The `chubock.java` role which is declared as a dependency for this role will try to install `Java` on the host. for more information about `chubock.java` visit [ansible-role-java](https://github.com/chubock/ansible-role-java). To set up an `Apache Zookeeper` cluster you can use `chubock.zookeeper` role. for more information visit [ansible-role-zookeeper](https://github.com/chubock/ansible-role-zookeeper)


Role Variables
--------------

The variable `borkerId` should be defined at host level and indicates the kafka broker id:

    [kafka]
    kfk1.local brokerId=1
    kfk2.local brokerId=2
    kfk3.local brokerId=3

The following variables are used for installation purposes:

where to install binaries:

    kafka_installation_path: /var/lib/kafka

where to download binaries from:

    kafka_url: https://downloads.apache.org/kafka/2.7.0/kafka_2.13-2.7.0.tgz
    
kafka configuration file template:
    
    kafka_config_template: server.properties.j2
    
kafka systemd service template:
    
    kafka_service_template: kafka.service.j2
    
whether to install prometheus exporter or not:
    
    prometheus_exporter: true
    
path to install prometheus binaries:
    
    prometheus_path: /var/lib/prometheus
        
java home path:
    
    java_home: /opt/java/default
    
    
to wait for the cluster to became stable or not:
    
    stable_rolling_update: true
    
number of retries for getting cluster state:

    stable_rolling_update_retries: 12
    
seconds to wait after each retry to get the cluster state:    
    
    stable_rolling_update_delay: 5

The following variables are kafka configuration properties. variables used for kafka configuration has the same name as kafka configuration properties:

    broker:
      id: "{{ brokerId }}"
    
    listeners: PLAINTEXT://:9092
    
    advertised:
      listeners: PLAINTEXT://{{ inventory_hostname }}:9092
    
    num:
      partitions: 5
      network:
        threads: 3
      io:
        threads: 8
      recovery:
        threads:
          per:
            data:
              dir: 1
    
    socket:
      send:
        buffer:
          bytes: 102400
      receive:
        buffer:
          bytes: 104857600
      request:
        max:
          bytes: 104857600
    
    log:
      dirs: /data/kafka
      retention:
        hours: 168
        check:
          interval:
            ms: 300000
      segment:
        bytes: 1073741824
    
    offsets:
      topic:
        replication:
          factor: 3
    
    transaction:
      state:
        log:
          replication:
            factor: 3
          min:
            isr: 2
    
    zookeeper:
      hosts: "{{groups['kafka']}}"
      port: 2181
      connection:
        timeout:
          ms: 6000
    
    delete:
      topic:
        enable: true
    
    default:
      replication:
        factor: 3
    
    min:
      insync:
        replicas: 2
    
    auto:
      create:
        topics:
          enable: false
    
    group:
      initial:
        rebalance:
          delay:
            ms: 0
    
Dependencies
------------

Role depends on `chubock.java` Role which as the name suggests, installs `Java` on the host.

Example Playbook
----------------

Including an example of how to use your role (for instance, with variables passed in as parameters) is always nice for users too:

    - hosts: kafka
      roles:
         - { role: chubock.kafka }

License
-------

BSD
