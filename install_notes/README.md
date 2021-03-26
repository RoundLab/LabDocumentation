# Description

Installation notes for non-standard or tricky to install pipelines, programs or packages.

## Anvio

[https://merenlab.org/2016/06/26/installation-v2/#3-install-anvio](https://merenlab.org/2016/06/26/installation-v2/#3-install-anvio)

Anvio is best setup in a Conda environment. Despite this, some issues are regularly encountered.

1. Follow Anvio's Conda environment setup directions to point #2. Then, activate the Anvio environment (use appropriate name/version for env you setup): `conda activate anvio-7`
2. Don't use pip to install Anvio though (point #3). Instead, install mamba first. `mamba install -y -c conda-forge -c bioconda anvio==7`
3. Install the mistune version as anvio notes: `pip install mistune==0.8.4`
4. Install greater than 0.22 version of scikit learn as module names have changed `pip install -U scikit-learn=0.22`
5. Test the install. It will run a bunch of tests and spit out a bunch of warnings, but at the end a browser window should pop up and ask you to allow connections if successful. `anvi-self-test` 

