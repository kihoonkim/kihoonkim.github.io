---
title: ELK Installation Guide v5
date: 2017-01-11 16:17:39
tags:
  - elasticsearch
  - kibana
  - logstash
  - filebeat
categories:
  - DevTools
  - ELK
thumbnail: /images/elasticsearch.png
---

➜  mkdir elkstack  
➜  cd elkstack  

# Elasticsearch
## install  
➜  curl -L -O https://artifacts.elastic.co/downloads/elasticsearch/elasticsearch-5.1.1.tar.gz  
➜  tar xzvf elasticsearch-5.1.1.tar.gz  
➜  cd elasticsearch-5.1.1  
## run
➜ bin/elasticsearch  
## test
http://localhost:9200?pretty=true  

# Kibana
## install
➜  curl -L -O https://artifacts.elastic.co/downloads/kibana/kibana-5.1.1-darwin-x86_64.tar.gz  
➜  tar xzvf kibana-5.1.1-darwin-x86_64.tar.gz  
➜  cd kibana-5.1.1-darwin-x86_64  

## config
➜  kibana-5.1.1-darwin-x86_64 vi config/kibana.yml  
```
(default)
# elasticsearch.url: "http://localhost:9200"
```
## run
➜  ./bin/kibana  
## test
http://localhost:5601  

# Logstash
## install
➜  curl -L -O https://artifacts.elastic.co/downloads/logstash/logstash-5.1.1.tar.gz  
➜  tar xzvf logstash-5.1.1.tar.gz  
## config
➜  vi config/logstash.conf  
```
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
```
## run
➜  bin/logstash -f config/logstash.conf  

# Filebeat
## install
➜  curl -L -O https://artifacts.elastic.co/downloads/beats/filebeat/filebeat-5.1.1-darwin-x86_64.tar.gz  
➜  tar xzvf filebeat-5.1.1-darwin-x86_64.tar.gz  
➜  cd filebeat-5.1.1-darwin-x86_64  
## filebeat.yml
```
filebeat.prospectors:
- input_type: log
  # Paths that should be crawled and fetched. Glob based paths.
  paths:
    - /tmp/*.log       <— Path변경
#-------------------------- Elasticsearch output ------------------------------
output.elasticsearch:
  # Array of hosts to connect to.
  #hosts: ["localhost:9200"]   <— 주석
#----------------------------- Logstash output --------------------------------
#output.logstash:
  # The Logstash hosts
  hosts: ["localhost:5044"]     <— 주석 해제
```
## dynamic template 설정
curl -XPUT 'http://localhost:9200/_template/filebeat?pretty' -d@filebeat.template.json  
```
{
  "acknowledged" : true
}
```
## run
➜  sudo ./filebeat -e -c filebeat.yml  
## test
➜  echo {"hello":"world"} > /tmp/mylog.log  
➜  echo {"hello":"world"} >> /tmp/mylog.log  
