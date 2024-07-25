# Dockerized Grafana with VictoriaMetrics

### Install Node Exporter

```shell
mkdir node_exporter
cd node_exporter/

wget https://github.com/prometheus/node_exporter/releases/download/v1.8.2/node_exporter-1.8.2.linux-amd64.tar.gz
tar xf node_exporter-1.8.2.linux-amd64.tar.gz

mv node_exporter-1.8.2.linux-amd64/node_exporter /usr/bin

# Check
node_exporter --version
```

Install using a systemd unit:

```shell
vim /etc/systemd/system/node_exporter.service

adduser node_exporter

mkdir -p /var/lib/node_exporter/textfile_collector

chown node_exporter:node_exporter /var/lib/node_exporter/textfile_collector

systemctl enable node_exporter.service
systemctl start node_exporter.service
systemctl status node_exporter.service
```

### Update binaries tools

```shell
mkdir vmutils && cd vmutils

wget https://github.com/VictoriaMetrics/VictoriaMetrics/releases/download/v1.102.0/vmutils-linux-amd64-v1.102.0.tar.gz
tar xf vmutils-linux-amd64-v1.102.0.tar.gz

cp ./vmalert-prod ../bin/vmalert
```

```shell
mkdir alertmanager-tools && cd alertmanager-tools

wget https://github.com/prometheus/alertmanager/releases/download/v0.27.0/alertmanager-0.27.0.linux-amd64.tar.gz
tar xf alertmanager-0.27.0.linux-amd64.tar.gz

mv ./alertmanager-0.27.0.linux-amd64/alertmanager ../bin/alertmanager
mv ./alertmanager-0.27.0.linux-amd64/amtool ../bin/amtool
```

```shell
mkdir fleet-utils && cd fleet-utils

wget https://github.com/fleetdm/fleet/releases/download/fleet-v4.54.0/fleetctl_v4.54.0_linux.tar.gz
tar xf fleetctl_v4.54.0_linux.tar.gz

wget https://github.com/fleetdm/fleet/releases/download/fleet-v4.54.0/fleet_v4.54.0_linux.tar.gz
tar xf fleet_v4.54.0_linux.tar.gz

mv ./fleetctl_v4.54.0_linux/fleetctl ../bin/fleetctl
mv ./fleet_v4.54.0_linux/fleet ../bin/fleet
```

### Run Fleet

```shell
docker-compose -f docker-compose-fleet.yml up -d

./bin/fleet prepare db \
  --mysql_address=127.0.0.1:4306 \
  --mysql_database=fleet \
  --mysql_username=fleet_user \
  --mysql_password=ChixxengahgheewayeiUUUKKK

openssl req -x509 -newkey rsa:4096 -sha256 -days 3650 -nodes \
  -keyout /tmp/server.key -out /tmp/server.cert -subj "/CN=SERVER_NAME” \
  -addext "subjectAltName=DNS:SERVER_NAME”

./bin/fleet serve \
  --mysql_address=127.0.0.1:4306 \
  --mysql_database=fleet \
  --mysql_username=fleet_user \
  --mysql_password=ChixxengahgheewayeiUUUKKK \
  --redis_address=127.0.0.1:7379 \
  --server_cert=/tmp/server.cert \
  --server_key=/tmp/server.key
```

### Misc

See list of alerts:

```shell
./bin/amtool --alertmanager.url http://localhost:9093/ alert query
```
