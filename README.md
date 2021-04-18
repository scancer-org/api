# Scancer API

[Scancer](https://scancer.org/) is a web application that uses
computer vision to assist with varying stages of breast cancer
detection. At its core is a collection of learnt detection models (in
[PyTorch](https://pytorch.org)) that are served as an API (using
[TorchServe](https://pytorch.org/serve/)).

The following documents different aspects of the API, from simply
using it in detection, to contributing new models, to monitoring the
API in production. Depending on your expertise and your goals, you
will find different sections of the documentation more or less
relevant.

If you are more interested in how this API is setup as part of the
entire stack, please refer instead to the [server configuration
scripts](https://github.com/scancer-org/setup/).

## How to use the API to detect breast cancer

The API is designed to be used as part of the primary webapp that
is hosted on [scancer.org](https://scancer.org/). But to enable its
use in your own applications, we also expose the API to the
web at [https://api.scancer.org/](https://api.scancer.org/). We have
locked this behind authentication to maintain some control of access
to it, but please [write to us](mailto:mail@harishnarayanan.org) if
you would like to use it. Send us a short description of your intended
usage and we will be happy to get you setup with credentials.

Once you have credentials, you can perform inference on medical images
to aid in cancer detection in the following way:

1. Browse the list of model repositories under the [Scancer GitHub
   Organisation](https://github.com/scancer-org/) and find the one
   most suitable to your requirements. These are consistently named
   `ml-<dataset>-<task>`, e.g. `ml-pcam-classification`for identifying
   metastatic cancer in small image patches taken from larger digital
   pathology scans trained on the [PatchCamelyon
   dataset](https://github.com/basveeling/pcam).
2. Now that you have the model you want to with, browse the repository
   to find more information about the format of the input images. The
   model repository will also contain test images for you to
   experiment with the API.
3. The endpoint you need to access for this model is systematically
   named `https://api.scancer.org/predictions/<dataset>-<task>`, and it
   always contains our best performing model for this task. You can
   perform inference with it as in the following example:
   ````
   curl --user <name>:<password> https://api.scancer.org/predictions/pcam-classification -T pcam-example.png
   ````

## How to contribute models to the API

TODO

How to contribute new cancer detection models to [Scancer’s model
store](https://model-store.scancer.org/models/). This is mostly
for domain experts and machine learning practitioners who wish to
contribute to the effort. 🙏


````
cd ~/api
mkdir model_store
wget https://download.pytorch.org/models/densenet161-8d451a50.pth
torch-model-archiver --model-name densenet161 --version 1.0 --model-file ./serve/examples/image_classifier/densenet_161/model.py --serialized-file densenet161-8d451a50.pth --export-path model_store --extra-files ./serve/examples/image_classifier/index_to_name.json --handler image_classifier
rm densenet161-8d451a50.pth
````

````
# Managing models via the API
curl -X GET  "http://localhost:8081/models
curl -X POST  "http://localhost:8081/models?url=https://torchserve.pytorch.org/mar_files/squeezenet1_1.mar"
curl -X GET  "http://localhost:8081/models
curl -X DELETE http://127.0.0.1:8081/models/squeezenet1_1
````

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
