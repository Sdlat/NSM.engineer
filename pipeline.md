#Logstash pipeline

  server side data processing pipeline
  ingest multiple data source simultaneously
  transform events into common formats
  enrich events with additional info
  output final results into a variety of destinations

  3 types of plugins: inputs, filters, output
  input: beats, kafka, syslog, tcp, udp jdbc
  filter: grok, dissect, http geoip, translate, fingerprint
  output: elasticsearch, redis, file, kafka, csv, stdout

  beats input port 5044

  mutate filter can rename among other stuff

  ##install, not testable

ssh into 172.16.50.100
sudo systemctl stop logstash
cd /etc/logstash/
ll
  vi pipelines.yml      just checking
sudo mkdir my-pipeline
sudo chown logstash: my-pipeline
cd my-pipeline
sudo vi input.conf
```
input {

  generator {

    message => '{"ts":1591367999.512593,"uid":"C5bLoe2Mvxqhawzqqd","id.orig_h":"192.168.4.76","id.orig_p":46378,"id.resp_h":"31.3.245.133","id.resp_p":80,"trans_depth":1,"method":"GET","host":"testmyids.com","uri":"/","version":"1.1","user_agent":"curl/7.47.0","request_body_len":0,"response_body_len":39,"status_code":200,"status_msg":"OK","tags":[],"resp_fuids":["FEEsZS1w0Z0VJIb5x4"],"resp_mime_types":["text/plain"]}'

    count => 1

  }

}


```


sudo vi filter.conf
```
filter {

  mutate {

    copy => { "[message]" => "[event][original]" }

  }


  json {

    source => "[message]"

    target => "[zeek][http]"

    remove_field => "[message]"

  }

  mutate {

    copy => { "[@timestamp]" => "[event][created]" }


    }

     date {

       match => [ "[zeek][http][ts]", "UNIX" ]

       target => "[@timestamp]"

       remove_field => "[zeek][http][ts]"

     }

  mutate {

   rename => {

     "[zeek][http][id.orig_h]" => "[source][address]"
     "[zeek][http][id.orig_p]" => "[source][port]"
     "[zeek][http][id.resp_h]" => "[destination][address]"
     "[zeek][http][id.resp_p]" => "[destination][port]"

   }


   }

  mutate {

    copy => {

      "[source][address]" => "[source][ip]"
      "[destination][address]" => "[destination][ip]"

    }

  }

  mutate {

    copy => {

      "[source][address]" => "[client][address]"
      "[destination][address]" => "[server][address]"
      "[source][port]" => "[client][port]"
      "[destination][port]" => "[server][port]"

    }

      }

  mutate {

    copy => {

      "[client][address]" => "[client][ip]"
      "[server][address]" => "[server][ip]"

    }

  }

  mutate {

    add_field => {

      "[ecs][version]" => "1.12"
      "[event][module]" => "zeek"
      "[event][dataset]" => "zeek.http"
      "[event][kind]" => "event"

    }

  }

  ruby {

    code => 'event.set("[event][category]", [ "network", "web" ])'

  }

    ruby {

      code => 'event.set("[event][type]", [ "connection" ])'

    }

  mutate {

    rename => {

      "[host]" => "[logstash][host]"

    }

    remove_field => [ "[@version]", "[sequence]", "[zeek][http][tags]" ]

  }
}
```
sudo /usr/share/logstash/bin/logstash --path.settings /etc/logstash/
vi output.conf
```
output {

 #stdout {}

  elasticsearch {

    hosts => "172.16.50.100"

    index => "test-index"

  }

}

```
rerun the long command









Copy paste of instructors filter.conf
'''
filter {

  mutate {

    copy => { "[message]" => "[event][original]" }

  }

  json {

    source => "[message]"

    target => "[zeek][http]"

    remove_field => "[message]"

  }

  mutate {

    copy => { "[@timestamp]" => "[event][created]" }

  }


  date {

    match => [ "[zeek][http][ts]", "UNIX" ]

    target => "[@timestamp]"

    remove_field => "[zeek][http][ts]"

 }


  mutate {

    rename => {

      "[zeek][http][id.orig_h]" => "[source][address]"
      "[zeek][http][id.orig_p]" => "[source][port]"
      "[zeek][http][id.resp_h]" => "[destination][address]"
      "[zeek][http][id.resp_p]" => "[destination][port]"

    }

  }

  mutate {

    copy => {

      "[source][address]"       => "[source][ip]"
      "[destination][address]"  => "[destination][ip]"

    }

  }

  mutate {

    copy => {

      "[source][address]"      => "[client][address]"
      "[destination][address]" => "[server][address]"

    }

  }

  mutate {

    copy => {

       "[client][address]" => "[client][ip]"
       "[server][address]" => "[server][ip]"

    }

  }

  mutate {

    copy => {

       "[source][port]"      => "[client][port]"
       "[destination][port]" => "[server][port]"

    }

  }

  mutate {

    add_field => {

      "[ecs][version]"    => "1.12"
      "[event][module]"   => "zeek"
      "[event][dataset]"  => "zeek.http"
      "[event][kind]"     => "event"

    }

  }

  ruby {

    code => 'event.set("[event][category]", [ "network", "web" ])'

  }

  ruby {

    code => 'event.set("[event][type]", [ "connection" ])'

  }


  mutate {

    rename => {

      "[zeek][http][uid]"                => "[zeek][session_id]"
      "[zeek][http][method]"             => "[http][request][method]"
      "[zeek][http][version]"            => "[http][version]"
      "[zeek][http][status_code]"        => "[http][response][status_code]"
      "[zeek][http][request_body_len]"   => "[http][request][body][bytes]"
      "[zeek][http][response_body_len]"  => "[http][response][body][bytes]"
      "[zeek][http][resp_mime_types]"    => "[http][response][mime_type]"
      "[zeek][http][host]"               => "[url][domain]"
      "[zeek][http][uri]"                => "[url][path]"
      "[zeek][http][user_agent]"         => "[user_agent][original]"

    }

  }

  mutate {

    rename => {

      "[host]" => "[logstash][host]"

    }

    remove_field => [ "[@version]", "[sequence]", "[zeek][http][tags]" ]

  }

}
'''
