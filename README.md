
# MATLAB Integration for *Jupyter* Reference Architecture

The Dockerfile in this repository can be used to build a Jupyter Image with the MATLAB Integration for Jupyter.

The images produced are based on `jupyter/base-notebook:ubuntu-22.04` which ships with `Python 3.11`.

## Build Instructions

### Get Sources
Access this Dockerfile either by directly downloading this repository from GitHub&reg;,
or by cloning this repository and
then navigating to the appropriate folder.
```bash
git clone https://github.com/mathworks-ref-arch/matlab-integration-for-jupyter.git
cd matlab-integration-for-jupyter
```
### Build & Run Docker Image
Build container with a name and tag of your choice.
```bash
docker build -t mifj:R2024b .
```

Run the container.
```bash
docker run -it -p 8888:8888 --rm mifj:R2024b
```

Visiting `http://<hostname>:8888/?token=<token>`, printed on the console, in a browser loads JupyterLab, where:

The hostname is the name of the computer running Docker.
The token is the secret token printed in the console.

For more information on running jupyter images, see [Jupyter Quick Start](https://jupyter-docker-stacks.readthedocs.io/en/latest/index.html#quick-start).

## Customize the Image

By default, the [Dockerfile](https://github.com/mathworks-ref-arch/matlab-integration-for-jupyter/blob/main/Dockerfile) installs MATLAB for the latest available MATLAB release without any additional toolboxes or products into the `/opt/matlab` folder, along with the Python package [jupyter-matlab-proxy](https://github.com/mathworks/jupyter-matlab-proxy) that provides the MATLAB Integration for Jupyter.

Use the options below to customize your build.

### Customize MATLAB Release, MATLAB Product List, MATLAB Install Location, License Server and other Python Packages
The [Dockerfile](https://github.com/mathworks-ref-arch/matlab-dockerfile/blob/main/Dockerfile) supports the following Docker build-time variables:

| Argument Name | Description | Default value |
|---|---|---|
| [MATLAB_RELEASE](#build-an-image-for-a-different-release-of-matlab) | The MATLAB release you want to install. | R2024b |
| [MATLAB_PRODUCT_LIST](#build-an-image-with-a-specific-set-of-products) | Products to install as a space-separated list. For more information, see [MPM.md](https://github.com/mathworks-ref-arch/matlab-dockerfile/blob/main/MPM.md). For example: MATLAB Simulink Deep_Learning_Toolbox Fixed-Point_Designer. | MATLAB |
| [MATLAB_INSTALL_LOCATION](#build-an-image-with-matlab-installed-to-a-specific-location) | The path to install MATLAB. | /opt/matlab |
| [LICENSE_SERVER](#build-an-image-configured-to-use-a-license-server) | The port and hostname of the machine that is running the Network License Manager, using the port@hostname syntax. For example: *27000@MyServerName* | *Unset* |
| [INSTALL_MATLABENGINE](#build-an-image-with-the-matlab-engine-for-python) | Set this value to install the MATLAB Engine for Python into the image. | *Unset* |
| [INSTALL_VNC](#build-an-image-with-the-matlab-integration-for-jupyter-using-vnc) | Set this value to install the [jupyter-matlab-vnc-proxy](https://github.com/mathworks/jupyter-matlab-vnc-proxy). Use MATLAB Integration for Jupyter using VNC to connect to a traditional MATLAB desktop via VNC.| *Unset* |


Use these arguments with the the `docker build` command to customize your image.

#### Build an Image for a Different Release of MATLAB
For example, to build an image for MATLAB R2019b, use this command.
```bash
docker build --build-arg MATLAB_RELEASE=R2019b -t mifj:R2019b .
```

#### Build an Image with a specific set of products
For example, to build an image with MATLAB and Simulink&reg;, use this command.
```bash
docker build --build-arg MATLAB_PRODUCT_LIST='MATLAB Simulink' -t mifj:R2024b .
```

#### Build an Image with MATLAB installed to a specific location
For example, to build an image with MATLAB installed at /opt/matlab, use this command.
```bash
docker build --build-arg MATLAB_INSTALL_LOCATION='/opt/matlab' -t mifj:R2024b .
```

#### Build an Image Configured to Use a License Server

Including the license server information with the `docker build` command means you do not have to pass it when running the container.
```bash
# Build container with the License Server.
docker build --build-arg LICENSE_SERVER=27000@MyServerName -t mifj:R2024b .

# Run the container, without needing to pass license information.
docker run -it --rm -p 8888:8888 mifj:R2024b 
```
Alternatively, to pass the License Server information at `docker run` time you can use environment variable `MLM_LICENSE_FILE` as shown below:
```bash
docker run -it --rm -p 8888:8888 -e MLM_LICENSE_FILE=27000@MyServerName mifj:R2024b
```

Please refer to [Use the Network License Manager](https://github.com/mathworks-ref-arch/matlab-dockerfile?tab=readme-ov-file#use-the-network-license-manager) for more information.

#### Build an Image with the MATLAB Engine for Python
For example, to build the default image along with the MATLAB Engine for Python for a given MATLAB Release.
```bash
docker build --build-arg INSTALL_MATLABENGINE=1 --build-arg MATLAB_RELEASE=R2024b -t mifj:R2024b .
```
Refer to the following links for more information:
- [MATLAB Engine for Python *(GitHub)*](https://github.com/mathworks/matlab-engine-for-python).
- On compatible Python versions, see [Python Comptability](https://mathworks.com/support/requirements/python-compatibility.html).

##### Special Considerations
* *This Dockefile uses an image based on Python 3.11 and the MATLAB Engine for Python only supports this version from MATLAB R2023b onwards.*
* *Failure to install the MATLAB Engine for Python will not fail the `docker build` command attempting to install it.*

#### Build an Image with the MATLAB Integration for Jupyter using VNC
For example, to build the default image along with the MATLAB Integration for Jupyter using VNC.
```bash
docker build --build-arg INSTALL_VNC=1 -t mifj:R2024b .
```
For more information, see [MATLAB Integration for Jupyter using VNC*(GitHub)*](https://github.com/mathworks/jupyter-matlab-vnc-proxy).

## Installing MATLAB
There are 3 ways to bring MATLAB into your container image.
1. Install MATLAB using [MATLAB Package Manager *(mpm)*](https://github.com/mathworks-ref-arch/matlab-dockerfile/blob/main/MPM.md).
1. [Mount MATLAB into your Image](#mounting-matlab-into-the-image).
1. [Bring Your Own Image *(BYOI)*](#bring-your-own-image)
    * Copy MATLAB installation from another container image.

### Installing MATLAB using *mpm*
The default configuration for the Dockerfile is to use *mpm* to install MATLAB and the specified toolboxes into the container image as described in the sections above.

### Mounting MATLAB into the Image
To provide a MATLAB installation to the container via a volume or bind mount, instead of installing MATLAB inside the image, follow the instructions below.

The Dockerfile assumes that MATLAB will be mounted into the `/opt/matlab` folder.
Use the Build Argument `MATLAB_INSTALL_LOCATION` to specify an custom location within the container onto which MATLAB is desired to be mounted onto.

#### Build Image
Use the [Build Arguments](https://docs.docker.com/reference/dockerfile/#arg) `MOUNT_MATLAB` and `MATLAB_RELEASE` with your `docker build` command.

```bash
docker build --build-arg MOUNT_MATLAB=1 \
             --build-arg MATLAB_RELEASE=R2024b \
             -t mifj:mounted .
```
The `MATLAB_RELEASE` argument, ensures that the system dependencies required for MATLAB, are installed into the container.

**NOTE:** The MATLAB Engine for Python cannot be installed at build time when MATLAB is being mounted into the container at runtime.

#### Specify mount location on container start up
If MATLAB is installed in `/usr/local/MATLAB/R2024b` on your local machine, you can bind mount this directory to `/opt/matlab` using the command shown below:
```bash
docker run -it --rm -v /usr/local/MATLAB/R2024b:/opt/matlab:ro -p 8888:8888 mifj:mounted 
```
For more information, see [Bind Mounts *(Docker)*](https://docs.docker.com/engine/storage/bind-mounts/).

Access the Jupyter Notebook by following one of the URLs displayed in the output of the docker run command.

This approach is useful when you want to minimize the size of the container as installing MATLAB and its toolboxes can make the image very large (greater than 10GB) depending on the MATLAB toolboxes installed.

### Bring Your Own Image
To copy an existing MATLAB installation from another container image, specify the image name with the Docker Build Arguments `MATLAB_IMAGE_NAME` and `MATLAB_RELEASE` as shown below:

```bash
# Copies MATLAB from the Dockerhub Image "mathworks/matlab:r2024b" into the image being built.
docker build --build-arg MATLAB_IMAGE_NAME=mathworks/matlab:r2024b \
             --build-arg MATLAB_RELEASE=R2024b \
             -t mifj:copied .
```
The `MATLAB_RELEASE` argument, ensures that the system dependencies required for MATLAB, are installed into the container.

## Pre-Built Images

Several Docker Images based on this Dockerfile are available for download from the *GitHub Container Registry*.

### jupyter-matlab-notebook
These images are based on `jupyter/base-notebook:ubuntu-22.04` and have the following available in them:
* MATLAB
* MATLAB Integration for Jupyter
* MATLAB Integration for Jupyter using VNC
* MATLAB Engine for Python 
    * Only available in pre-built images newer than R2023b
    * See [Different Versions of OS or Python](#different-versions-of-os-or-python) for more information on installing the engine for older versions of MATLAB.

**Available Tags**: `R2024b`, `R2024a`, `R2023b`, `R2023a`, `R2022b`

**Docker Pull Command**:
```bash
# Substitute the tag with your desired version of MATLAB.
docker pull ghcr.io/mathworks-ref-arch/matlab-integration-for-jupyter/jupyter-matlab-notebook:R2024b
```
### jupyter-mounted-matlab-notebook
These images are based on `jupyter/base-notebook:ubuntu-22.04` and have the following available in them:
* MATLAB Integration for Jupyter
* MATLAB Integration for Jupyter using VNC
* MATLAB Engine for Python 
    * Only available in pre-built images newer than R2023b
    * See [Different Versions of OS or Python](#different-versions-of-os-or-python) for more information on installing the engine for older versions of MATLAB.

**Available Tags**: `R2024b`, `R2024a`, `R2023b`, `R2023a`, `R2022b`

**Docker Pull Command**:
```bash
# Substitute the tag with your desired version of MATLAB.
docker pull ghcr.io/mathworks-ref-arch/matlab-integration-for-jupyter/jupyter-mounted-matlab-notebook:R2024b
```
Use the correct version of the image based on the MATLAB Release you are mounting into the image.
For example to mount `R2022b` from your local machine that is installed into `/usr/local/MATLAB/R2022b` use the following `docker run` command:
```bash
docker run -it --rm -v /usr/local/MATLAB/R2022b:/opt/matlab:ro -p 8888:8888 ghcr.io/mathworks-ref-arch/matlab-integration-for-jupyter/jupyter-mounted-matlab-notebook:R2022b
```

## Different Versions of OS or Python

To build on older versions of Ubuntu or Python, update the base image used in the Dockerfile with the tags presented in the [jupyter/docker-stacks *(GitHub)*](https://github.com/jupyter/docker-stacks?tab=readme-ov-file#using-old-images) page.

For example, the [jupyter/docker-stacks *(GitHub)*](https://github.com/jupyter/docker-stacks?tab=readme-ov-file#using-old-images) page lists `4d70cf8da953` as the tag for an image with `Python 3.10` on `Ubuntu 22.04`.

To build and image with this base layer, update this [line](https://github.com/mathworks-ref-arch/matlab-integration-for-jupyter/blob/main/Dockerfile#L66) in the Dockerfile to `FROM quay.io/jupyter/base-notebook:4d70cf8da953`.

This might be useful if your workflow depends on older versions of Python. 
For example the MATLAB Engine for Python only supports Python 3.11 from R2023b.

MATLAB releases older than R2023b would require an older versions of Python, and you could use the approach above to build an image with the desired python version.
See [python compatibility for MATLAB Engine for Python](https://mathworks.com/support/requirements/python-compatibility.html) for more information.

### MATLAB Dependencies
Refer the [MATLAB Dependencies](https://github.com/mathworks-ref-arch/container-images/tree/main/matlab-deps) repository to verify that the release of MATLAB you are installing is supported on the Operating System you choose. 
For example, R2024b is currently the only MATLAB Release which supports Ubuntu 24.04. 

## Support & Feedback
If you encounter a technical issue or have an enhancement request.

Please create an [issue](https://github.com/mathworks-ref-arch/matlab-integration-for-jupyter/issues/new) with the relevant information for us to reproduce and investigate the reported issue.

----

Copyright 2020-2024 The MathWorks, Inc.

----
