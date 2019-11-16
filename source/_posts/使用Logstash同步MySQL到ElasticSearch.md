---
title: 使用Logstash同步MySQL到ElasticSearch
date: 2019-11-13 11:47:09
tags:
- Logstash
- ElasticSearch
- MySQL
categories:
- ElasticSearch
---
mysql 作为成熟稳定的数据持久化解决方案，广泛地应用在各种领域，但是MySQL在进行进行非常庞大的数据检索时速度会比较慢，而 elasticsearch可以作为一项解决方案，而我们要做的只需要将 mysql 中的数据同步到 elasticsearch 中即可。  
这里使用的同步工具为**Logstash**
##### Logstash下载
Logstash官网下载地址：[logstash](https://www.elastic.co/products/logstash)  
下载的版本尽量和elasticsearch版本号一致
##### 编写配置文件
在config文件夹里创建mysql.conf配置文件
```
input {
  jdbc {
    jdbc_driver_library => "C:/Users/11469/Desktop/logstash-6.2.2/logstash-6.2.2/mysql-connector-java-5.1.6.jar"
    jdbc_driver_class => "com.mysql.jdbc.Driver"
    jdbc_connection_string => "jdbc:mysql://127.0.0.1:3306/blog"
    jdbc_user => "root"
	
    jdbc_password => "root"
	clean_run => true
    schedule => "* * * * *"
	
    statement => "SELECT * FROM t_blog WHERE update_time > :sql_last_value AND update_time < NOW() ORDER BY update_time desc"
  }
}
output {
  elasticsearch {
    hosts => ["127.0.0.1:9200"]
    index => "blog"
    document_id => "%{id}"
  }
}
```
``jdbc_driver_library``JDBC驱动的路径  
``jdbc_driver_class``驱动类的名字  
``jdbc_connection_string``mysql 地址  
``jdbc_user``mysql 用户  
``jdbc_password``mysql 密码  
``schedule``执行 sql 时机，类似 crontab 的调度  
``clean_run``是否清除 last_run_metadata_path 的记录,如果为真那么每次都相当于从头开始查询所有的数据库记录
##### 启动logstash
去下载下来的logstash的bin目录执行
```
logstash -f ../config/mysql.conf```

程序就以mysql.conf为配置文件进行启动，当结果出现
```
[2019-11-13T11:42:21,409][INFO ][logstash.outputs.elasticsearch] Installing elasticsearch template to _template/logstash
[2019-11-13T11:42:32,595][INFO ][logstash.outputs.elasticsearch] New Elasticsearch output {:class=>"LogStash::Outputs::ElasticSearch", :hosts=>["//127.0.0.1:9200"]}
[2019-11-13T11:42:32,898][INFO ][logstash.pipeline        ] Pipeline started succesfully {:pipeline_id=>"main", :thread=>"#<Thread:0x12eea893 sleep>"}
[2019-11-13T11:42:32,984][INFO ][logstash.agent           ] Pipelines running {:count=>1, :pipelines=>["main"]}
[2019-11-13T11:43:01,843][INFO ][logstash.inputs.jdbc     ] (0.103000s) SELECT version()
[2019-11-13T11:43:01,974][INFO ][logstash.inputs.jdbc     ] (0.087000s) SELECT * FROM t_blog WHERE update_time > '1970-01-01 00:00:00' AND update_time < NOW() ORDER BY update_time desc
```
正常执行，MySQL中的数据就同步到了ElasticSearch
##### 报错
如果结果出现
```
[2019-11-13T11:40:33,754][INFO ][logstash.runner          ] Starting Logstash {"logstash.version"=>"6.2.2"}
[2019-11-13T11:40:34,073][INFO ][logstash.agent           ] Successfully started Logstash API endpoint {:port=>9600}
[2019-11-13T11:40:34,241][ERROR][logstash.agent           ] Failed to execute action {:action=>LogStash::PipelineAction::Create/pipeline_id:main, :exception=>"LogStash::ConfigurationError", :message=>"Expected one of #, {, } at line 12, column 76 (byte 423) after input {\n  jdbc {\n    jdbc_driver_library => \"C:/Users/11469/Desktop/logstash-6.2.2/logstash-6.2.2/mysql-connector-java-5.1.6.jar\"\n    jdbc_driver_class => \"com.mysql.jdbc.Driver\"\n    jdbc_connection_string => \"jdbc:mysql://127.0.0.1:3306/blog\"\n    jdbc_user => \"root\"\n\t\n    jdbc_password => \"root\"\n\tclean_run => true\n    schedule => \"* * * * *\"\n\t\n    statement => \"SELECT * FROM t_blog WHERE update_time > :sql_last_value\"", :backtrace=>["C:/Users/11469/Desktop/logstash-6.2.2/logstash-6.2.2/logstash-core/lib/logstash/compiler.rb:42:in `compile_imperative'", "C:/Users/11469/Desktop/logstash-6.2.2/logstash-6.2.2/logstash-core/lib/logstash/compiler.rb:50:in `compile_graph'", "C:/Users/11469/Desktop/logstash-6.2.2/logstash-6.2.2/logstash-core/lib/logstash/compiler.rb:12:in `block in compile_sources'", 
```
中有``LogStash::ConfigurationError``,则说明mysql.conf出现了错误，检查一下是否出现地址写错，忘了加""之类