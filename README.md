# Dockerized Grafana with VictoriaMetrics

### Install Node Exporter

```bash
mkdir node_exporter
cd node_exporter/

wget https://github.com/prometheus/node_exporter/releases/download/v1.7.0/node_exporter-1.7.0.linux-amd64.tar.gz
tar xf node_exporter-1.7.0.linux-amd64.tar.gz

mv node_exporter-1.7.0.linux-amd64/node_exporter /usr/bin

# Check
node_exporter --version
```

Install using a systemd unit:

```bash
vim /etc/systemd/system/node_exporter.service

adduser node_exporter

mkdir -p /var/lib/node_exporter/textfile_collector

chown node_exporter:node_exporter /var/lib/node_exporter/textfile_collector

systemctl enable node_exporter.service
systemctl start node_exporter.service
systemctl status node_exporter.service
```

### Misc

See list of alerts:

```bash
./bin/amtool --alertmanager.url http://localhost:9093/ alert query
```
