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

The API is designed to be used as part of the primary webapp that is
hosted on [scancer.org](https://scancer.org/). But to enable its use
in your own applications, we also expose the API to the web at
[https://api.scancer.org/](https://api.scancer.org/). We have locked
this behind authentication to maintain some control of access to it,
but please [write to us][email] if you would like to use it. Send us a
short description of your intended usage and we will be happy to get
you setup with credentials.

Once you have credentials (`name, password`), you can perform
inference on medical images to aid in cancer detection in the
following way:

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
   perform inference with it by `PUT`ting an image to it as in the
   following example:
   ````
   curl --user <name>:<password> https://api.scancer.org/predictions/pcam-classification -T pcam-example.png
   ````
   The server will respond with a response that corresponds to the
   task at hand. e.g. In this case, it will return 0 (no metastatic
   cancer) or 1 (presence of metastatic cancer somewhere in the input
   image).

## How to contribute to the API

If you are a domain expert or machine learning practitioner who wishes
to contribute to our efforts, thank you! 🙏 There are various ways you
can help.

### Help with manual training data and domain insight

If you are a domain expert (physician, pathologist, radiologist, ...)
and wish to help with this project, please [let us know][email]. We
will get you in touch with teams working on data modelling who would
be happy to use your assistance with better training our models.

You can also get in touch if you would like to user [prototypes of our
application](https://scancer.org/) and provide feedback to improve its
usage in a clinical setting.

### Contribute new detection models (or improve existing ones)

To improve an existing model, you can clone its corresponding
repository from
`https://github.com/scancer-org/ml-<dataset>-<task>`. Modify the model
or hyperparameters of the existing model and submit a pull request if
you find that its performance characteristics are better than the
existing case.

To submit a new model, please use one of the existing models as a
template for how to setup your repository. Do write to us before you
get started on your work so that we can tell you early on how best to
get your work incorporated into the project. Once you are done with
training and documenting your model, we will work with you to
incorporate it as a model under the umbrella of the organisation and
give you access to our model store to upload your trained model.

#### How to manage models in our model store

##### Generate an archive of your best model

1. Output the weights of your PyTorch model in a very specific way,
   following this example for `pcam-classification`:
   ````
   example_input = torch.rand(1, 3, 96, 96).to(device)
   traced_script_module = torch.jit.trace(model, example_input)
   traced_script_module.save("pcam-classification.pt")
   ````

2. Write a handler file to deal with pre and post-processing of image
   data. Follow the template at
   [ml-pcam-classification/todo/pcam-classification-handler.py](TODO).

3. Archive this into a TorchServe-friendly format

   ````
   torch-model-archiver --model-name pcam-classification --version 1.0 --serialized-file pcam-classification.pt --handler pcam_classifier_handler.py
   ````

4. Use the model store REST API upload (and manage) your models

   ````
   # Get a list of existing models
   curl -X GET  "http://localhost:8081/models

   # Get the details of a specific model

   # Upload a specific model archive
   curl -X POST  "http://localhost:8081/models?url=https://torchserve.pytorch.org/mar_files/squeezenet1_1.mar"

   # Remove a specific model
   curl -X DELETE http://127.0.0.1:8081/models/squeezenet1_1

   # Scale up and down the workers
   TODO
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

## Copyright and license

Copyright (c) 2021 [Harish Narayanan](https://harishnarayanan.org) and
Daniel Hen.

This code is licenced under the MIT Licence. See
[LICENSE](https://github.com/scancer-org/api/blob/main/LICENSE) for
the full text of this licence.

[email]: mailto:mail@harishnarayanan.org
