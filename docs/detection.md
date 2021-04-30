# How to use the Scancer API to detect breast cancer

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
