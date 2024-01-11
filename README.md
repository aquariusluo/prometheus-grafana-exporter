# Near Prometheus Exporter

This service exports various metrics from Near node for consumption by [Prometheus](https://prometheus.io). It uses [JSON-RPC](https://docs.near.org/docs/interaction/rpc) interface to collect the metrics. Inspired by [Ethereum Prometheus Exporter](https://github.com/31z4/ethereum-prometheus-exporter)

## Install Docker
```
sudo apt-get update
sudo apt install docker.io
```

## Usage

You can deploy using the [masknetgoal634/near-prometheus-exporter](https://hub.docker.com/r/masknetgoal634/near-prometheus-exporter) Docker image.

```
sudo docker run -dit \
    --restart always \
    --name near-exporter \
    --network=host \
    -p 9333:9333 \
    masknetgoal634/near-prometheus-exporter:latest /dist/main -accountId <YOUR_POOL_ID>
```


### Build own image

    git clone https://github.com/aquariusluo/prometheus-grafana-exporter

    cd prometheus-grafana-exporter

    sudo docker build -t prometheus-grafana-exporter .

```
sudo docker run -dit \
    --restart always \
    --name near-exporter \
    --network=host \
    -p 9333:9333 \
    near-prometheus-exporter:latest /dist/main -accountId <YOUR_POOL_ID>
```

By default the exporter serves on `:9333` at `/metrics`.

## License

This project is licensed under the MIT License - see the [LICENSE](LICENSE) file for details.
