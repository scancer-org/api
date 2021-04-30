## How to monitor the API

TODO

How to [monitor Scancer’s API](https://monitoring.scancer.org/)
performance.

````
# Model metrics can be accessed via https://pytorch.org/serve/metrics_api.html
curl http://127.0.0.1:8082/metrics
wget https://github.com/prometheus/prometheus/releases/download/v2.26.0/prometheus-2.26.0.linux-amd64.tar.gz
tar -xzf prometheus-2.26.0.linux-amd64.tar.gz
mv prometheus-2.26.0.linux-amd64 prometheus
rm prometheus-2.26.0.linux-amd64.tar.gz
cd prometheus
# Create a prometheus config file
./prometheus --config.file=prometheus.yml
````
