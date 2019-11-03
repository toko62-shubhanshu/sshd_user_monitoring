# SSDH_USER_MONITORING
It showing metrics of number of users ssh on server, there successful attempts and unsuccessful 

Grok_Exporter
=============

Export [Prometheus] metrics from arbitrary unstructured log data.

About Grok
----------

[Grok] is a tool to parse crappy unstructured log data into something structured and queryable. Grok is heavily used in [Logstash] to provide log data as input for [ElasticSearch].

Grok ships with about 120 predefined patterns for syslog logs, apache and other webserver logs, mysql logs, etc. It is easy to extend Grok with custom patterns.

The `grok_exporter` aims at porting Grok from the [ELK stack] to [Prometheus] monitoring. The goal is to use Grok patterns for extracting Prometheus metrics from arbitrary log files.

How to run the example
----------------------

Download `grok_exporter-$ARCH.zip` for your operating system from the [releases] page, extract the archive, `cd grok_exporter-$ARCH`, then run

```bash
./grok_exporter -config config.yml
```

The exporter provides the metrics on [http://localhost:9144/metrics]:

Configuration
-------------

Example configuration:

```yaml
global:
    config_version: 2
input:
    type: file
    path: /var/log/auth.log
    readall: true # Read from the beginning of the file? False means we start at the end of the file and read only new lines.
grok:
    patterns_dir: ./patterns
metrics:
    - type: counter
      name: user_activityi_alphaserver1
      help: Total number of Alphaserver1 ssh attempt in server
      match: 'session opened for user alphaserver1'
    - type: counter
      name: user_activity_alphaserver2
      help: Total number of Alphaserver2 ssh attempt in server
      match: 'session opened for user alphaserver2'

server:
    host: localhost
    port: 9144
```

The metrics is exposing like this 
```
# TYPE user_activity_alphaserver2 counter
user_activity_alphaserver2 4
# HELP user_activityi_alphaserver1 Total number of Alphaserver1 ssh attempt in server
# TYPE user_activityi_alphaserver1 counter
user_activityi_alphaserver1 1
```

PROMETHEUS SETUP
---------------- 

Prometheus is a very powerful open-source monitoring and alerting system suitable for dynamic environments like in Cloud space. It is a standalone project maintained by the community with many companies and organizations contributing to its source code

Downloading the prometheus

```
$ sudo su -
$ export RELEASE="2.2.1"
$ wget https://github.com/prometheus/prometheus/releases/download/v${RELEASE}/prometheus-${RELEASE}.linux-amd64.tar.gz
```

After downloading the archive, extract it using tar.

```
$ tar xvf prometheus-${RELEASE}.linux-amd64.tar.gz
$ cd prometheus-${RELEASE}.linux-amd64/
```

Add the new exporter in Prometheus Config 

```
root@tkp-tesla:~/prometheus-2.2.1.linux-amd64# cat prometheus.yml
# my global config
global:
  scrape_interval:     15s # Set the scrape interval to every 15 seconds. Default is every 1 minute.
  evaluation_interval: 15s # Evaluate rules every 15 seconds. The default is every 1 minute.
  # scrape_timeout is set to the global default (10s).

# Alertmanager configuration
alerting:
  alertmanagers:
  - static_configs:
    - targets:
      # - alertmanager:9093

# Load rules once and periodically evaluate them according to the global 'evaluation_interval'.
rule_files:
  # - "first_rules.yml"
  # - "second_rules.yml"

# A scrape configuration containing exactly one endpoint to scrape:
# Here it's Prometheus itself.
scrape_configs:
  # The job name is added as a label `job=<job_name>` to any timeseries scraped from this config.
  - job_name: 'prometheus'

    # metrics_path defaults to '/metrics'
    # scheme defaults to 'http'.

    static_configs:
      - targets: ['localhost:9090']

  - job_name: 'grok_exporter'
    scrape_interval: 5s
    static_configs:
       - targets: ['localhost:9144']
```

then run the prometheus service

```bash
   ./prometheus
```

To get Web interface, open http://ip:9090


GRAFANA SETUP
--------------

Run this list command to install grafana 

```bash
   sudo add-apt-repository "deb https://packages.grafana.com/oss/deb stable main"
   wget -q -O - https://packages.grafana.com/gpg.key | sudo apt-key add -
   sudo apt-get update
   sudo apt-get install grafana
```

Start the server (init.d service)¶

```
  sudo service grafana-server start
```
Open the grafana in web UI and add the Datasource(+) with name grok_exporter

Logging in for the first time
To run Grafana open your browser and go to http://localhost:3000/. 3000 is the default HTTP port
