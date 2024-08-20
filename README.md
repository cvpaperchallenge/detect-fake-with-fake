# Ascender

![stable](https://img.shields.io/badge/stable-v0.1.3-blue)
![python versions](https://img.shields.io/badge/python-3.8%20%7C%203.9%20%7C%203.10%20%7C%203.11%20%7C%203.12-blue)
[![tests](https://github.com/cvpaperchallenge/Ascender/actions/workflows/lint-and-test.yaml/badge.svg)](https://github.com/cvpaperchallenge/Ascender/actions/workflows/lint-and-test.yaml)
[![MIT License](https://img.shields.io/github/license/cvpaperchallenge/Ascender?color=green)](LICENSE)
[![Ruff](https://img.shields.io/endpoint?url=https://raw.githubusercontent.com/astral-sh/ruff/main/assets/badge/v2.json)](https://github.com/astral-sh/ruff)
[![Typing: mypy](https://img.shields.io/badge/typing-mypy-blue)](https://github.com/python/mypy)
[![DOI](https://zenodo.org/badge/466620310.svg)](https://zenodo.org/badge/latestdoi/466620310)

## What is Ascender?

Ascender (Accelerator of SCiENtific DEvelopment and Research) is a [GitHub repository template](https://docs.github.com/en/repositories/creating-and-managing-repositories/creating-a-template-repository) designed for research projects using Python. It incorporates several pre-implemented features to expedite development:

- **Containerization**: Dependency minimization and code portability enhancement using [Docker](https://www.docker.com/).
- **Virtual Environment / Package Management**: Development environment reproducibility ensured by [Poetry](https://python-poetry.org/).
- **Coding Style**: Automatic code linting and formatting with [Ruff](https://docs.astral.sh/ruff/).
- **Static Type Checking**: Early bug detection assisted by [Mypy](https://github.com/python/mypy).
- **Testing**: Testing simplification achieved through [pytest](https://github.com/pytest-dev/pytest).
- **GitHub Integration**: Integration features including [GitHub Actions workflows](https://docs.github.com/en/actions/using-workflows/workflow-syntax-for-github-actions), issue templates, and more.

Please also view [resources about Ascender (in Japanese)](https://cvpaperchallenge.github.io/Britannica/ascender/ja).

## Project Organization

```
    ├── .github/               <- GitHub settings.
    │
    ├── data/                  <- Datasets.
    │
    ├── environments/          <- Environment-specific configurations.
    │
    ├── models/                <- Pretrained and serialized models.
    │
    ├── notebooks/             <- Jupyter notebooks.
    │
    ├── outputs/               <- Outputs.
    │
    ├── src/                   <- Python Source code.
    │
    ├── tests/                 <- Test code.
    │
    ├── .dockerignore
    ├── .gitignore
    ├── LICENSE
    ├── Makefile               <- Commands for task automation.
    ├── poetry.lock            <- Auto-generated lock file (do not edit manually).
    ├── poetry.toml            <- Poetry configuration.
    ├── pyproject.toml         <- Main project configuration file.
    └── README.md              <- Top-level README for developers.
```

## Prerequisites

- [Docker](https://www.docker.com/)
- [Docker Compose](https://github.com/docker/compose)
- (Optional) [NVIDIA Container Toolkit (nvidia-docker2)](https://github.com/NVIDIA/nvidia-docker)

> \[!Note\]
> The example codes in the README.md are written for `Docker Compose v2`. However, Ascender is also compatible with `Docker Compose v1`. If you are using `Docker Compose v1`, simply replace `docker compose` with `docker-compose` in the example commands.

## Prerequisites Installation

This section provides installation instructions for Ubuntu. If you have already installed the prerequisites, you may skip this section. For installations on other operating systems, please refer to the official documentation.

- Docker and Docker Compose: [Install Docker Engine](https://docs.docker.com/engine/install/)
- NVIDIA Container Toolkit (nvidia-docker2): [Installation Guide](https://docs.nvidia.com/datacenter/cloud-native/container-toolkit/install-guide.html#docker)

### Install Docker and Docker Compose

```bash
# Set up the repository
$ sudo apt update
$ sudo apt install ca-certificates curl gnupg lsb-release
$ sudo mkdir -p /etc/apt/keyrings
$ curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo gpg --dearmor -o /etc/apt/keyrings/docker.gpg
$ echo \
  "deb [arch=$(dpkg --print-architecture) signed-by=/etc/apt/keyrings/docker.gpg] https://download.docker.com/linux/ubuntu \
  $(lsb_release -cs) stable" | sudo tee /etc/apt/sources.list.d/docker.list > /dev/null

# Install Docker and Docker Compose
$ sudo apt update
$ sudo apt install docker-ce docker-ce-cli containerd.io docker-compose-plugin
```

If `sudo docker run hello-world` works, the installation was successful.

### (Optional) Install NVIDIA Container Toolkit

To use GPUs with Ascender, install the NVIDIA Container Toolkit as well. This toolkit has specific prerequisites, detailed in the [official documentation](https://docs.nvidia.com/datacenter/cloud-native/container-toolkit/install-guide.html#pre-requisites).

```bash
$ distribution=$(. /etc/os-release;echo $ID$VERSION_ID) \
      && curl -fsSL https://nvidia.github.io/libnvidia-container/gpgkey | sudo gpg --dearmor -o /usr/share/keyrings/nvidia-container-toolkit-keyring.gpg \
      && curl -s -L https://nvidia.github.io/libnvidia-container/$distribution/libnvidia-container.list | \
            sed 's#deb https://#deb [signed-by=/usr/share/keyrings/nvidia-container-toolkit-keyring.gpg] https://#g' | \
            sudo tee /etc/apt/sources.list.d/nvidia-container-toolkit.list

$ sudo apt update
$ sudo apt install -y nvidia-docker2
$ sudo systemctl restart docker
```

If `sudo docker run --rm --gpus all nvidia/cuda:11.0.3-base nvidia-smi` works, the installation was successful.

## Quick Start

This section outlines how to begin using Ascender. For more detailed information, please refer to [this slide (in Japanese)](https://cvpaperchallenge.github.io/Britannica/ascender/ja).

### Create a GitHub Repository from Ascender

To start, you need to create your own GitHub repository from Ascender:

- Visit the GitHub repository page of Ascender.
- Click the ["Use this template"](https://github.com/cvpaperchallenge/Ascender/generate) button at the top right of the page.
- Complete the form and click the "Create repository from template" button.

Your new repository should now be set up in your GitHub account.

### (Optional) Modify Names Used in the Template

In the Ascender template, names used in the system by default, such as those for Docker containers and bind-mounted volumes, are set to "ascender." Particularly when using Ascender’s template to create and manage multiple containers, to avoid name conflicts between containers, it is necessary to change the container names for each project (refer also to the FAQ section "Building multiple containers with Ascender’s template").

If you want to change the default names used in the system, please modify the value of `PROJECT_NAME_ENV` in `environments/[cpu,gpu,ci]/.env`.

### (Optional) Set Environment Variables for Development Within the Container

Depending on the services, frameworks, and libraries used during development, it may be necessary to specify API keys, database hostnames, and passwords as environment variables. To use environment variables within the container, please follow the steps below.

- Copy `environments/envs.env.sample` to create `environments/envs.env`

- Edit `environments/envs.env` to set the environment variables you want to use inside the container

- Add the `env_file` option to `environments/[cpu,gpu]/docker-compose.yaml`, and specify the path to `environments/envs.env` that was created above (by uncommenting the section that is commented out by default)."

  ```yaml
  # env_file:       # <- uncomment here
  #   - ../envs.env # <- uncomment here
  ```

> \[!Note\]
> The `envs.env` file may contain sensitive information such as API keys and passwords and should not be version-controlled by Git. In Ascender, files named `*.env` are excluded from Git tracking by default, as they are listed in the `.gitignore` file.

### Start Development

```bash
# Clone the repository
$ git clone git@github.com:cvpaperchallenge/<YOUR_REPO_NAME>.git
$ cd <YOUR_REPO_NAME>

# Build the Docker image and run the container
$ cd environments/gpu  # For CPU only, navigate to `environments/cpu`
$ sudo docker compose up -d

# Enter the container shell
$ sudo docker compose exec core bash

# Set up the virtual environment and install dependencies with Poetry
$ poetry install
```

You are now ready to start developing with Ascender.

### Stop Development

```bash
# Stop the container
$ cd environments/gpu  # Or `cd environments/cpu`
$ sudo docker compose stop
```

## FAQ

### Using Ascender Without Docker

While we recommend using Docker as described, you may encounter issues installing Docker due to permissions or other constraints.

If you cannot use Docker, Ascender can be operated without it. Simply install Poetry on your computer and proceed as described in the "Start Development" section, omitting the Docker steps.

```bash
# Install Poetry
$ pip3 install poetry

# Clone the repository
$ git clone git@github.com:<YOUR_USER_NAME>/<YOUR_REPO_NAME>.git
$ cd <YOUR_REPO_NAME>

# Set up the virtual environment and install dependencies with Poetry
$ poetry install
```

> \[!Note\]
> The CI jobs in Ascender's GitHub Actions workflows utilize a Dockerfile. Running without Docker may cause these jobs to fail, necessitating modifications to the Dockerfile or the deletion of the CI job (`.github/workflows/lint-and-test.yaml`).

### Permission Errors When Running `poetry install`

Sometimes, running `poetry install` may result in a permission error:

```bash
$ poetry install
...

virtualenv: error: argument dest: the destination . is not write-able at /home/challenger/ascender
```

If this occurs, check your local PC's UID (user ID) and GID (group ID) with the following commands:

```bash
$ id -u $USER  # Check UID
$ id -g $USER  # Check GID
```

In Ascender, the default UID and GID are both '1000'. If your local PC's UID or GID differs from this, you'll need to adjust the 'UID' or 'GID' values in 'docker-compose.yaml' to match your local settings. Alternatively, if the 'HOST_UID' and 'HOST_GID' environment variables are set on your host PC, Ascender will use these values.

### Compatibility Issues Between PyTorch and Poetry

> \[!Note\]
> Now poetry 1.2 is used in Ascender. So this issue is expected to be solved.

As of now, there is a known compatibility issue between PyTorch and Poetry, which the Poetry community is actively addressing. This issue is anticipated to be resolved in Poetry version 1.2.0. You can track progress and explore pre-releases of this version [here](https://github.com/python-poetry/poetry/releases/tag/1.2.0b3).

We plan to integrate Poetry 1.2.0 into Ascender as soon as it becomes available. In the meantime, you may need to use workarounds detailed in [this issue](https://github.com/python-poetry/poetry/issues/4231).

**Related GitHub Issues**

- [https://github.com/python-poetry/poetry/issues/2339](https://github.com/python-poetry/poetry/issues/2339)
- [https://github.com/python-poetry/poetry/issues/2543](https://github.com/python-poetry/poetry/issues/2543)
- [https://github.com/python-poetry/poetry/issues/2613](https://github.com/python-poetry/poetry/issues/2613)
- [https://github.com/python-poetry/poetry/issues/3855](https://github.com/python-poetry/poetry/issues/3855)
- [https://github.com/python-poetry/poetry/issues/4231](https://github.com/python-poetry/poetry/issues/4231)
- [https://github.com/python-poetry/poetry/issues/4704](https://github.com/python-poetry/poetry/issues/4704)

### Changing Python Versions for CI Jobs

By default, Ascender's CI jobs run using Python 3.8 and 3.9. If you wish to target a different Python version, modify [the matrix in `.github/workflows/lint-and-test.yaml`](https://github.com/cvpaperchallenge/Ascender/blob/master/.github/workflows/lint-and-test.yaml#L18).

### Incorrect Reflection of Dockerfile Changes in Image Builds

If you find that changes to the Dockerfile are not reflected when building the image, try the following commands:

```bash
$ sudo docker compose build --no-cache
$ sudo docker compose up --force-recreate -d
```

When changes to the Dockerfile are not reflected correctly, the potential reasons could be:

Docker uses a cache to build the image.
Docker does not recreate a container.
The `sudo docker compose build --no-cache` command builds the Docker image without using the cache, addressing the first issue. The `sudo docker compose up --force-recreate -d` command recreates and starts the containers, addressing the second issue.

### Activating/Deactivating Caching in CI Jobs

Caching was introduced in CI jobs (`lint-and-tests.yaml`) starting from `v0.1.2` to reduce delays caused by Docker image builds and Poetry installations. However, if you prefer not to use this feature, set the `USE_CACHE` variable in `lint-and-tests.yaml` to `false`.

### Excessive Strictness of Ruff's Code Style Constraints

If you find the style checks enforced by Ruff too stringent, you can adjust the settings in `pyproject.toml` under `tool.ruff.[xxx]`.

- `select`: Specify which Ruff style rules to apply.
- `ignore`: Set rules to be ignored during style checking.
- `fixable`: Allow automatic correction for certain fixable rules.
- `unfixable`: Specify rules that should not be automatically corrected.

For details on each rule, please refer to [here](https://docs.astral.sh/ruff/rules/). For more information on how to configure the `pyproject.toml` file, see [here](https://docs.astral.sh/ruff/settings/).

### Building multiple containers with Ascender’s template

When using Ascender's templates for multiple projects, the following additional settings are necessary:

- Specify port numbers: To avoid specifying the same port numbers as the existing containers, change the host PC's port in `environments/[cpu,gpu,ci]/docker-compose.yaml` from the default value.

  ```yaml
  ports:
      - 8000:8000 # Example: Change to 8001:8000
  ```

- Change the project name: To prevent conflicting with the existing container names, change `PROJECT_NAME_ENV` in `environments/[cpu,gpu,ci]/.env`.

  ```text
  # If you need to change the default name of the project, edit the following.
  PROJECT_NAME_ENV=ascender # Example: Change to a new project name
  ```
