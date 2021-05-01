# How to contribute to the Scancer breast cancer detection API and model store

If you are a domain expert in or machine learning practitioner who wishes
to contribute to our efforts, thank you! 🙏 There are various ways you
can help.

## Help with manual training data and domain insight

If you are a domain expert (physician, pathologist, radiologist, ...)
and wish to help with this project, please [let us know][email]. We
will get you in touch with teams working on data modelling who would
be happy to use your assistance with better training our models.

You can also get in touch if you would like to use [prototypes of our
application](https://scancer.org/) and provide feedback to increase its
relevancy to a clinical setting.

## Contribute new detection models (or improve existing ones)

### To improve an existing deep learning model

1. Please [write to us][email] before get started on your work, so
that we can tell you early on what is most useful and how best to get
your work incorporated into the project.
2. Clone its corresponding repository from
`https://github.com/scancer-org/ml-<dataset>-<task>`.
3. Modify the model architecture or hyperparameters, train and test
your modified model.
4. If you find that your model’s performance characteristics are better
than the existing model, please submit a pull request with your
improvements.
5. Read the [following
section](#package-your-models-for-uploading-to-the-model-store) to
learn how to package your new model and upload it to our model store.

### To submit a new model

1. Please [write to us][email] before get started on your work, so
that we can tell you early on what is most useful and how best to get
your work incorporated into the project.
2. Use one of the [existing model](https://github.com/scancer-org)
 repositories as a template for how to setup your repository.
3. Develop, train, test your model.
4. Carefully document it and provide example data for testing it.
5. Let us know once you are done, and we will work with you to
incorporate it as a model under the umbrella of the organisation, and
give you access to our model store to upload your trained
model.
6. Read the [following
section](#package-your-models-for-uploading-to-the-model-store) to
learn how to package your new model and upload it to our model store.

### Package your models for uploading to the model store

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

3. Setup Torch Model Archiver:

   ```
   pip install torchserve torch-model-archiver
   ```

4. Archive your model into a [TorchServe-friendly
   format](https://github.com/pytorch/serve/tree/master/model-archiver#torch-model-archiver-for-torchserve):

   ````
   torch-model-archiver --model-name pcam-classification \
                        --version 1.0 \
                        --serialized-file pcam-classification.pt \
                        --handler pcam_classifier_handler.py
   ````

5. Upload your model archive to a suitable place on the web. If it is
   small enough (tens of megabytes), you can commit it directly to
   your GitHub repository as in our example at
   [https://github.com/scancer-org/ml-pcam-classification/tree/main/models](https://github.com/scancer-org/ml-pcam-classification/tree/main/models).

   If it is larger, you can consider [Git
   LFS](https://git-lfs.github.com) or any other mechanism to host
   your file (e.g. [Amazon S3](https://aws.amazon.com/s3/).

## How to manage models in the Scancer model store

The Scancer Project’s breast cancer detection model store can be found
at
[https://model-store.scancer.org/](https://model-store.scancer.org/). We have
locked this behind authentication to maintain some control of access
to it, but please [write to us][email] if you would like to use
it. Send us a short description of your intended usage and we will be
happy to get you setup with credentials.

Once you have credentials (`username, password`), you can interact
with it using [a standard gRPC API][ts-management-api]. For each of
the following commands, you will be prompted for your `password`.


### Get a list of existing models

````
$ curl -u username https://model-store.scancer.org/models/

{
  "models": [
    {
      "modelName": "pcam-classification",
      "modelUrl": "https://github.com/scancer-org/ml-pcam-classification/raw/main/models/pcam-classification.mar"
    }
  ]
}
````

### Get the details of a specific model

````
$ curl -u username https://model-store.scancer.org/models/pcam-classification

[
  {
    "modelName": "pcam-classification",
    "modelVersion": "1.1",
    "modelUrl": "https://github.com/scancer-org/ml-pcam-classification/raw/main/models/pcam-classification.mar",
    "runtime": "python",
    ...
  }
]
````

### Upload your packaged model archive

````
$ curl -X POST -u username https://model-store.scancer.org/models/?url=https://github.com/scancer-org/ml-pcam-classification/raw/main/models/pcam-classification.mar

{
  "status": "Model \"pcam-classification\" Version: 1.1 registered with 0 initial workers. Use scale workers API to add workers for the model."
}
````

### Scale up (and down) workers for this model

````
$ curl -X PUT -u username https://model-store.scancer.org/models/pcam-classification?min_worker=2&max_worker=2

{
  "status": "Processing worker updates..."
}

# Wait for a few seconds, and confirm that the workers are setup
$ curl -u username https://model-store.scancer.org/models/pcam-classification

[
  {
    "modelName": "pcam-classification",
    "modelVersion": "1.1",
    "modelUrl": "https://github.com/scancer-org/ml-pcam-classification/raw/main/models/pcam-classification.mar",
    ...
    "workers": [
      {
        "id": "9000",
        "startTime": "2021-05-01T11:46:23.500Z",
        "status": "READY",
        "memoryUsage": 0,
        "pid": 395591,
        "gpu": false,
        "gpuUsage": "N/A"
      },
      {
        "id": "9001",
        "startTime": "2021-05-01T11:46:23.501Z",
        "status": "READY",
        "memoryUsage": 0,
        "pid": 395588,
        "gpu": false,
        "gpuUsage": "N/A"
      }
    ]
  }
]
````

### Remove a specific model

⛔ Please be very careful when attempting this.

````
$ curl -X DELETE -u username https://model-store.scancer.org/models/pcam-classification

{
  "status": "Model \"pcam-classification\" unregistered"
}
````

[email]: mailto:mail@harishnarayanan.org
[ts-management-api]: https://github.com/pytorch/serve/blob/master/docs/management_api.md#management-api
