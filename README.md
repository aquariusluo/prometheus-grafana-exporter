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

### Build own image

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

  - job_name: "namada"
    scrape_interval: 5s
    metrics_path: /
    static_configs:
      - targets: ['IP:26660']
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

### Run Grafana

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

## License

This project is licensed under the MIT License - see the [LICENSE](LICENSE) file for details.
