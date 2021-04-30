# Scancer API

[Scancer](https://scancer.org/) is a web application that uses
computer vision to assist with varying stages of breast cancer
detection. At its core is a collection of learnt detection models (in
[PyTorch](https://pytorch.org)) that are served as an API (using
[TorchServe](https://pytorch.org/serve/)).

The specific procedures that are used to diagnose breast cancer are
depicted in the graphic below. And Scancer aims to have
state-of-the-art models to assist with all the procedures denoted in
purple.

![Tests and procedures used to diagnose breast cancer](docs/images/cancer-diagnosis-deep-learning.svg)

The documentation in this repository shows you:

1. [How to use these models for detection](docs/detection.md)
2. [How to improve on existing models, or contribute new ones](docs/contributing.md)
3. [How to monitor the API in production](docs/monitoring.md)

Depending on your expertise and your goals, you will find different
sections of the documentation more or less relevant. If you are more
interested in how this API is setup as part of the entire stack,
please refer instead to the [server configuration
scripts](https://github.com/scancer-org/setup/).

## Copyright and license

Copyright (c) 2021 [Harish Narayanan](https://harishnarayanan.org) and
Daniel Hen.

This code is licenced under the MIT Licence. See
[LICENSE](https://github.com/scancer-org/api/blob/main/LICENSE) for
the full text of this licence.

[email]: mailto:mail@harishnarayanan.org
