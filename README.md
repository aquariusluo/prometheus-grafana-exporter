# Namada Prometheus Exporter

This service exports various metrics from Near node for consumption by [Prometheus](https://prometheus.io). It uses [JSON-RPC](https://docs.near.org/docs/interaction/rpc) interface to collect the metrics. Inspired by [Ethereum Prometheus Exporter](https://github.com/31z4/ethereum-prometheus-exporter)

## Install Docker
```
sudo apt-get update
sudo apt install docker.io
```
## Add ${USER} to docker group
```
sudo groupadd docker
sudo usermod -aG docker ${USER}
sudo chown ${USER}:docker /var/run/docker.sock
sudo systemctl restart docker
```
## Clear unnecessary images
```
docker ps -a
docker images
docker rm <container_name_or_id>
docker image prune -af
```
## Run Node Exporter on the Namada Node

First we need to deploy [namada prometheus exporter](https://github.com/aquariusluo/prometheus-grafana-exporter) service to collect custom metrics from the namada node using json-rpc.

```
sudo docker run -dit \
    --restart always \
    --volume /proc:/host/proc:ro \
    --volume /sys:/host/sys:ro \
    --volume /:/rootfs:ro \
    --name node-exporter \
    -p 9100:9100 prom/node-exporter:latest \
    --path.procfs=/host/proc \
    --path.sysfs=/host/sys
```

Open 9100 port in your server firewall as Prometheus reads metrics on this port.

## Build own image on your server monitoring machine

    git clone https://github.com/aquariusluo/prometheus-grafana-exporter

    cd prometheus-grafana-exporter/etc  

    open `prometheus/prometheus.yml` and add an ip address of your node:

```
  ...
scrape_configs:
  # The job name is added as a label `job=<job_name>` to any timeseries scraped from this config.
  - job_name: 'prometheus'

    # metrics_path defaults to '/metrics'
    # scheme defaults to 'http'.

    static_configs:
    - targets: ['localhost:9090']

  - job_name: node
    scrape_interval: 5s
    static_configs:
    - targets: ['<NODE_IP_ADDRESS>:9100']

  - job_name: "namada"
    scrape_interval: 15s
    static_configs:
      - targets: ['<NODE_IP_ADDRESS>:26660']
  ...
```

## Run Prometheus on your server monitoring machine

```
sudo docker run -dti \
    --restart always \
    --volume $(pwd)/prometheus:/etc/prometheus/ \
    --name prometheus \
    --network=host \
    -p 9090:9090 prom/prometheus:latest \
    --config.file=/etc/prometheus/prometheus.yml
```
Open in your favorite browser `http://IP:9090`   

## Run Grafana

open `grafana/custom.ini` and add your gmail address

```
...
#################################### SMTP / Emailing ##########################
[smtp]
enabled = true
host = smtp.gmail.com:587 
user = <your_gmail_address>
# If the password contains # or ; you have to wrap it with triple quotes. Ex """#password;"""
password = <your_gmail_password>
;cert_file =
;key_file =
skip_verify = true
from_address = <your_gmail_address>
from_name = Grafana
# EHLO identity in SMTP dialog (defaults to instance_name)
;ehlo_identity = dashboard.example.com
# SMTP startTLS policy (defaults to 'OpportunisticStartTLS') 
;startTLS_policy = NoStartTLS
```

```
id -u
-> 1000

sudo chown -R 1000:1000 grafana/*
```
```
sudo docker run -dit \
    --restart always \
    --volume $(pwd)/grafana:/var/lib/grafana \
    --volume $(pwd)/grafana/provisioning:/etc/grafana/provisioning \
    --volume $(pwd)/grafana/custom.ini:/etc/grafana/grafana.ini \
    --user 1000 \
    --network=host \
    --name grafana \
    -p 3000:3000 grafana/grafana
```

Open in your favorite browser `http://IP:3000`

![](https://raw.githubusercontent.com/masknetgoal634/near-prometheus-exporter/master/guide/img/image0.png)

Username: admin
Password: admin

## MISC

Check docker logs
```
sudo docker exec -it <Name or ID> bash
sudo docker logs <Name or ID>
```

Open firewall ports
```
sudo ufw allow 9090
sudo ufw allow 3000
```


## License

This project is licensed under the MIT License - see the [LICENSE](LICENSE) file for details.
