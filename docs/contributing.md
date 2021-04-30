# How to contribute to the Scancer API

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
