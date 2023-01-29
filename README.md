


#### how you can add ELK stack (Elasticsearch, Logstash, and Kibana) in a Spring Boot application to monitor production logs::


* Add the following dependencies in your **pom.xml** file:

```

<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-web</artifactId>
</dependency>
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-logging</artifactId>
</dependency>
<dependency>
    <groupId>net.logstash.logback</groupId>
    <artifactId>logstash-logback-encoder</artifactId>
    <version>6.1</version>
</dependency>


```

* Create a **logstash.conf** file in the **resources** folder of your project. This file will be used to configure Logstash to listen for log events from your application:


```


input {
    tcp {
        port => 5000
        codec => json_lines
    }
}
output {
    elasticsearch {
        hosts => ["http://elasticsearch:9200"]
    }
}



```

* Create a **logback-spring.xml** file in the **resources** folder of your project. This file will be used to configure logback to send log events to Logstash:

```

<configuration>
    <appender name="logstash" class="net.logstash.logback.appender.LogstashTcpSocketAppender">
        <destination>logstash:5000</destination>
        <encoder class="net.logstash.logback.encoder.LoggingEventCompositeJsonEncoder">
            <providers>
                <timestamp>
                    <timeZone>UTC</timeZone>
                </timestamp>
                <pattern>
                    <pattern>
                        {
                            "severity": "%level",
                            "service": "your-service-name",
                            "trace": "%X{X-B3-TraceId:-},%X{X-B3-SpanId:-},%X{X-Span-Export:-}",
                            "span": "%X{X-B3-SpanId:-}",
                            "exportable": "%X{X-Span-Export:-}",
                            "pid": "${PID:-}",
                            "thread": "%thread",
                            "class": "%logger{40}",
                            "rest": "%message"
                        }
                    </pattern>
                </pattern>
            </providers>
        </encoder>
    </appender>
    <root level="info">
        <appender-ref ref="logstash"/>
    </root>
</configuration>


```


* Create a **docker-compose.yml** file in the root of your project. This file will be used to define the services that make up your application, including Elasticsearch, Logstash, and Kibana:


```

version: '3'
services:
 

```

