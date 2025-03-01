# About this project
This project is a simple Springboot 3 project which sends logs via TCP to a logstash server to be displayed in Kibana.


## How to run
ELK needs 3 services/components started, [Elasticsearch](https://www.elastic.co/es/downloads/elasticsearch), [Kibana](https://www.elastic.co/downloads/kibana), [Logstash](https://www.elastic.co/es/downloads/logstash). Please manually download each one on your machine.

**1. Start up elasticsearch:**
elasticsearch comes with security enable by default, I found that could be a problem if you only want to run and test quickly your application. So, first we need to Desable security features in the <ELASTICSEARCH_FILES>./config/elasticsearch.yml, this will make our Kibana configuration easy.
```
# Enable security features
xpack.security.enabled: false
xpack.security.enrollment.enabled: false
```
**2. Start up Kibana**, if you do not disable security, you will be asked to set connect to elasticsearch
 If you work with security enable, then you will need to reset the password for kibana_system
```
in the elasticsearch files
./bin/elasticsearch-reset-password --username kibana_system --url https://localhost:9200 -f
```
**3. Start up logstash:**
You need to edit you <LOGSTASH_FILES>./config/logstash-sample.conf to tell logstash that the input will be TCP. Here you can look how my config file looks like.
Once you have your config file done, you should start logstash this way:
```
./bin/logstash -f ./config/logstash-sample.conf
```

Content of logstash-sample.conf
```
# Sample Logstash configuration for creating a simple
# Beats -> Logstash -> Elasticsearch pipeline.

input {
  tcp {
    port => 5044
    codec => json_lines  # Use json_lines codec to decode JSON logs
  }
}

filter {
  # Optional: Additional filters can be added here
}

output {
  stdout {
    codec => json
  }
  elasticsearch {
    hosts => ["http://localhost:9200"]
    user => "elastic"
    password => "cT80hm6O"
  }
}
```
4. Run you springboot application. This app has a logback-spring.xml which set up an appender for sending metrics through TCP to the logstash server(12.0.0.1:5044)
```
mvn spring-boot:run
```
5. After running you application you should start to see the logs in kibana, you could invoke the localhost:8080/test endpoint to add more logs
![img.png](img.png)

## Videos
1. [How to quickly configure ELK+Springboot](https://studio.youtube.com/video/7kUGt3Ikd7Y/edit)
2. [How to configure ELK locally(Security enabled)](https://youtu.be/v01PMLEaTMc)
3. [ELK+Springboot fixing invalid cert path](https://youtu.be/gz1BFDx3uZc)



## Links and references
1. https://medium.com/@sovisrushain/monitoring-spring-boot-microservices-logs-with-the-elk-stack-aeeaf3e98d7b
