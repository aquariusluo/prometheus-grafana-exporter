# Near Prometheus Exporter

This service exports various metrics from Near node for consumption by [Prometheus](https://prometheus.io). It uses [JSON-RPC](https://docs.near.org/docs/interaction/rpc) interface to collect the metrics. Inspired by [Ethereum Prometheus Exporter](https://github.com/31z4/ethereum-prometheus-exporter)

## Install Docker
```
sudo apt-get update
sudo apt install docker.io
```
## Add USER to Docker Group
```
sudo groupadd docker
sudo usermod -aG docker ${USER}
sudo chown ${USER}:docker /var/run/docker.sock
sudo systemctl restart docker
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
      - targets: ['<NAMADA_NODE_IP>:26660']
  ...
```

By default the exporter serves on `:9333` at `/metrics`.

## License

This project is licensed under the MIT License - see the [LICENSE](LICENSE) file for details.
