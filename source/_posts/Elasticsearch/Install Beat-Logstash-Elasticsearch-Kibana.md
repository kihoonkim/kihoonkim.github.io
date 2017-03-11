---
title: ELK Installation Guide v2
date: 2016-01-20 16:17:39
tags:
  - elasticsearch
  - kibana
  - logstash
  - filebeat
categories: ELK
thumbnail: /images/elasticsearch.png
---
# E(B)LK Installation Guide

![fig](http://cfile1.uf.tistory.com/image/227DAC3C562F0EAD34BF60 "BELK")
- Log Collection : [log file] -> [file beat] -----> [logstash] -> [elasticseach]  
- Visualization  : [Kibana] -> [elasticseach]  

## �����غ�
~~~
# JDK & JAVA_HOME
$ sudo apt-get install openjdk-7-jre
~~~

## Elasticsearch ��ġ
1. Download and unzip the latest Elasticsearch distribution : [Elasticsearch 2.0.0](https://download.elasticsearch.org/elasticsearch/release/org/elasticsearch/distribution/zip/elasticsearch/2.0.0/elasticsearch-2.0.0.zip)
~~~
$ curl -L -O  https://download.elasticsearch.org/elasticsearch/release/org/elasticsearch/distribution/tar/elasticsearch/2.0.0/elasticsearch-2.0.0.tar.gz
$ tar xzvf elasticsearch-2.0.0.tar.gz
~~~

2. Run  
~~~
$ bin/elasticsearch
~~~

3. Test  
~~~
http://localhost:9200?pretty=true
~~~

## Kibana Installation
1. Download and unzip Kibana 4.X : [Kibana 4.2.1](https://download.elastic.co/kibana/kibana/kibana-4.2.1-windows.zip)
~~~
$ curl -L -O https://download.elastic.co/kibana/kibana/kibana-4.2.1-linux-x64.tar.gz
$ tar xzvf kibana-4.2.1-linux-x64.tar.gz
$ cd kibana-4.2.1-linux-x64/
~~~

2. edit config/kibana.yml  
~~~
(default)
# elasticsearch.url: "http://localhost:9200"
~~~

3. Run  
~~~
./bin/kibana
~~~

4. Test  
~~~
http://localhost:5601
~~~

## Logstash(Log Aggregation) ��ġ  
1. Download and unzip the latest Logstash release  : [Logstash 2.0.0](https://download.elastic.co/logstash/logstash/logstash-2.0.0.zip)
~~~
$ curl -L -O https://download.elastic.co/logstash/logstash/logstash-2.0.0.tar.gz
$ tar xzvf logstash-2.0.0.tar.gz
~~~

2. create a config file  
~~~
# config/logstash.conf
input {
  beats {
    port => 5044
  }
}
output {
  elasticsearch {
    hosts => "localhost:9200"
    sniffing => true
    manage_template => false
    index => "%{[@metadata][beat]}-%{+YYYY.MM.dd}"
    document_type => "%{[@metadata][type]}"
  }
}
~~~

3. plugin updates  
~~~
$ ./plugin update logstash-input-beats
or
$ ./plugin uninstall logstash-input-beats
$ ./plugin install logstash-input-beats
~~~

4. Run  
~~~
$ ./logstash agent -f ../config/logstash.conf
~~~

## Filebeat(Log forwarder) ��ġ
1. Download and install or unzip Filebeat : [Filebeat 1.0.0](https://download.elastic.co/beats/filebeat/filebeat-1.0.0-rc2-windows.zip)
~~~
$ curl -L -O https://download.elastic.co/beats/filebeat/filebeat-1.0.0-rc2-x86_64.tar.gz
$ sudo tar xzvf filebeat-1.0.0-rc2-x86_64.tar.gz
~~~

2. Edit the filebeat.yml  
~~~
#filebeat.yml
...
  prospectors:
    ...
      paths:
        - /tmp/*.log
    ...
  output:
    #elasticsearch:
      #hosts: ["localhost:9200"]
    ...
    logstash:
      hosts: ["localhost:5044"]
~~~

3. dynamic template ���� : filebeat.template.json  
~~~
$ curl -XPUT 'http://localhost:9200/_template/filebeat?pretty' -d@filebeat.template.json
~~~

4. Run
~~~
$ sudo ./filebeat -e -c filebeat.yml
~~~

5. Test
~~~
$ echo {"hello":"world"} > /tmp/mylog.log
~~~
