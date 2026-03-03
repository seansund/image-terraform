# Terraform container image

[![Docker Repository on Quay](https://quay.io/repository/cloudnativetoolkit/terraform/status "Docker Repository on Quay")](https://quay.io/repository/cloudnativetoolkit/terraform)

This repository builds a container image with a minimal set of tools required to apply Terraform automation.

The container includes the following:

- bash
- terraform cli
- terragrunt cli
- git cli
- jq cli
- yq v4 cli

**Warning: The material contained in this repository has not been thoroughly tested. Proceed with caution.**

## Getting started

### Prerequisites

To run this image, the following tools are required:

- `docker` cli
- `docker` backend - Docker Desktop, colima, etc

### Running the client

Start the client to use it.

- To run the `terraform` container:

    ```bash
    docker run -itd --name terraform quay.io/cloudnativetoolkit/terraform
    ```

Once the client is running in the background, use it by opening a shell in it.

- To use the `terraform` container, exec shell into it:

    ```bash
    docker exec -it terraform /bin/bash
    ```

  Your terminal is now in the container.

Use this shell to run commands using the installed tools and scripts.

When you're finished running commands, to exit the client.

- To leave the `terraform` container shell, as with any shell:

    ```bash
    exit
    ```

  The container will keep running after you exit its shell.

If the client stops:

- To run the `terraform` container again:

    ```bash
    docker start terraform
    ```

The `terraform` container is just a Docker container, so all [Docker CLI commands](https://docs.docker.com/engine/reference/commandline/cli/) work.

## Container registry

The build automation pushes the built container image to [quay.io/cloudnativetoolkit/terraform](https://quay.io/cloudnativetoolkit/terraform)

### Floating tags

The floating image tags use the following convention:

- `latest` - the latest **alpine** version of the image (currently terraform v1.2)
- `alpine` - the latest **alpine** version of the image (currently terraform v1.2)
- `fedora` - the latest **fedora** version of the image (currently terraform v1.2)
- `v1.2` - the latest **alpine** version of the image using terraform v1.2
- `v1.1` - the latest **alpine** version of the image using terraform v1.1
- `v1.0` - the latest **alpine** version of the image using terraform v1.0
- `v1.2-alpine` - the latest **alpine** version of the image using terraform v1.2
- `v1.1-alpine` - the latest **alpine** version of the image using terraform v1.1
- `v1.0-alpine` - the latest **alpine** version of the image using terraform v1.0
- `v1.2-fedora` - the latest **fedora** version of the image using terraform v1.2
- `v1.1-fedora` - the latest **fedora** version of the image using terraform v1.1
- `v1.0-fedora` - the latest **fedora** version of the image using terraform v1.0

### Pinned tags

Each release within the repository corresponds to a pinned image tag that will never be moved to another image. The pinned tags use the following naming convention:

```text
{terraform version}-{release tag}-{base OS image}
```

where:

- `{terraform version}` - is the major and minor version of the terraform cli (e.g. v1.1)
- `{release tag}` - is the release tag for this repository (e.g. v1.0.0)
- `{base OS image}` - is the base OS image (`alpine` or `fedora`)

For example:

```text
v1.1-v1.0.0-alpine
```

## Usage

The image can be used by referring to the image url. The following can be used to run the container image interactively:

```shell
docker run -it quay.io/cloudnativetoolkit/terraform
```

## Development

To build the default image using the latest version of terraform on alpine, run the following:

```shell
docker build -t terraform .
```

### Changing terraform versions

The terraform version can be changed by passing the `TERRAFORM_VERSION` as a build arg. For example:

```shell
docker build --build-arg TERRAFORM_VERSION=1.1.9 -t terraform:v1.1 .
```

### Changing base OS versions


The base OS can be changed by using the `Dockerfile-fedora` file. For example:

```shell
docker build -f Dockerfile-fedora -t terraform:fedora .
```

## Testing

The repository includes configuration files to test the built container images using the [container-structure-test](https://github.com/GoogleContainerTools/container-structure-test) utility.

Several configuration files are defined:

- `config.yaml` - provides validation for the majority of the container image structure
- `config-v1.0.yaml` - provides validation for the terraform cli version in v1.0 builds of the image  
- `config-v1.1.yaml` - provides validation for the terraform cli version in v1.1 builds of the image  
- `config-v1.2.yaml` - provides validation for the terraform cli version in v1.2 builds of the image  

The config files can be used in combination by providing multiple `--config` arguments to the **container-structure-test** cli. 

### Prerequisites

Install the **container-structure-test** cli by following the instructions found on [container-structure-test](https://github.com/GoogleContainerTools/container-structure-test).

**Note:** If you are using **colima**, the `DOCKER_HOST` environment variable will need to be set first.  

1. Run `colima status` to get the socket value (e.g. `unix:///Users/myuser/.colima/default/docker.sock`) 
2. Export the socket value in the `DOCKER_HOST` environment variable - `export DOCKER_HOST="unix:///Users/myuser/.colima/default/docker.sock"`)

### Run the tests

Run the test by providing the uri to the image and the config file(s) that will be applied. The image can exist locally or on a remote container registry.

```shell
container-structure-test test --image {image name} --config config.yaml --config {version config}
```

For example:

```shell
container-structure-test test --image quay.io/cloudnativetoolkit/terraform:v1.0-alpine --config config.yaml --config config-v1.0.yaml
```

**Note:** If you want to test a image from a remote container registry, the `--pull` argument must be included.
