# Use MATLAB Integration for Jupyter in a Docker Container

The `Dockerfile` in this repository builds a Jupyter® Docker® image which contains the `jupyter-matlab-proxy` package and MATLAB®. This package allows you to open a MATLAB desktop in a web browser tab, directly from your Jupyter environment.


The MATLAB Integration for Jupyter is under active development and you might find issues with the MATLAB graphical user interface. For support or to report issues, see the [Feedback](#Feedback) section.


If you want to install the MATLAB Integration for Jupyter without using Docker, use the installation instructions in the repository
[MATLAB Integration for Jupyter](https://github.com/mathworks/jupyter-matlab-proxy) instead.

## Requirements

Before starting, make sure you have [Docker](https://docs.docker.com/get-docker/) installed on your system.

## Instructions

The `Dockerfile` in this repository builds upon the base image `jupyter/base-notebook`. Optionally, you can customize the `Dockerfile` to build upon any alternative Jupyter Docker base image.

To build the Docker image, follow these steps:

1. Select a base container image with a MATLAB R2020b or later (64 bit Linux®) installation. If you need to create one, follow the steps at [Create a MATLAB Container Image](https://github.com/mathworks-ref-arch/matlab-dockerfile).

2. Open the `Dockerfile`, replace the name `matlab` with the name of the MATLAB image from step 1:

   ```
   FROM matlab AS matlab-install-stage
   ```

3. MATLAB must be on the system path in the image built in step 1.
   If not, then edit the following line to point to your MATLAB installation:

   ```
   ARG BASE_ML_INSTALL_LOC=/tmp/matlab-install-location
   ```

   For example, if MATLAB is installed at the location `/opt/matlab/R2020b` change the line above to:

   ```
   ARG BASE_ML_INSTALL_LOC=/opt/matlab/R2020b
   ```

4. (Optional) To preconfigure a network license manager, open the `Dockerfile`, uncomment the line below, and replace `port@hostname` with the licence server address:

   ```
    # ENV MLM_LICENSE_FILE port@hostname
   ```

5. Build a Docker image labelled `matlab-notebook`, using the file `Dockerfile`:

   ```bash
   docker build -t matlab-notebook .
   ```

6. Start a Docker container, and
forward the default Jupyter web-app port (8888) to the host machine:

   ```bash
   docker run -it -p 8888:8888 matlab-notebook
   ```

Access the Jupyter Notebook by following one of the URLs displayed in the output of the ```docker run``` command.
For instructions about how to use the integration, see [MATLAB Integration for Jupyter](https://github.com/mathworks/jupyter-matlab-proxy).

#### Advanced

Installing MATLAB into the Docker image can make the image very large (greater than 10GB) depending on the MATLAB toolboxes installed.
To make the image smaller, you can give the Docker container access to a MATLAB installation using a volume or bind mount. For more details see [Provide MATLAB as a Volume or Bind Mount](/matlab/MATLAB_mounted.md).

## Feedback

We encourage you to try this repository with your environment and provide feedback – the technical team is monitoring this repository. If you encounter a technical issue or have an enhancement request, send an email to `jupyter-support@mathworks.com`.

