ARG BASE_IMAGE=ubuntu:20.04
FROM ${BASE_IMAGE}

ARG PROJECT_NAME=ascender
ARG USER_NAME=challenger
ARG GROUP_NAME=challengers
ARG UID=1000
ARG GID=1000
ARG PYTHON_VERSION=3.12
ARG APPLICATION_DIRECTORY=/home/${USER_NAME}/${PROJECT_NAME}
ARG RUN_POETRY_INSTALL_AT_BUILD_TIME="false"

ENV DEBIAN_FRONTEND="noninteractive" \
    LC_ALL="C.UTF-8" \
    LANG="C.UTF-8" \
    PYTHONPATH=${APPLICATION_DIRECTORY}

RUN apt update -y && apt install --no-install-recommends -y software-properties-common \
    # Add deadsnakes PPA to install older Python versions
    && add-apt-repository ppa:deadsnakes/ppa \
    && apt-get update && apt-get install -y \
    # Install specified Python
    python${PYTHON_VERSION} python${PYTHON_VERSION}-dev python3-pip python3-dev python-is-python3 \
    build-essential openssl libssl-dev libffi-dev ca-certificates git curl make ssh openssh-client \
    && update-ca-certificates \
    && apt-get clean \
    && rm -rf /var/lib/apt/lists/*

# Swtich default python3 version
RUN update-alternatives --install /usr/bin/python python /usr/bin/python${PYTHON_VERSION} 1 \
    && update-alternatives --install /usr/bin/python3 python3 /usr/bin/python${PYTHON_VERSION} 1

# Install pip for specified Python version
RUN curl -sS https://bootstrap.pypa.io/get-pip.py | python${PYTHON_VERSION}

RUN python3 -m pip install --upgrade pip setuptools requests wheel six urllib3 \
    && python3 -m pip install poetry

# Add user. Without this, following process is executed as admin.
RUN groupadd -g ${GID} ${GROUP_NAME} \
    && useradd -ms /bin/sh -u ${UID} -g ${GID} ${USER_NAME}

USER ${USER_NAME}
WORKDIR ${APPLICATION_DIRECTORY}

# If ${RUN_POETRY_INSTALL_AT_BUILD_TIME} = "true", install Python package by Poetry and move .venv under ${HOME}.
# This process is for CI (GitHub Actions). To prevent overwrite by volume of docker compose, .venv is moved under ${HOME}.
COPY --chown=${UID}:${GID} pyproject.toml poetry.lock poetry.toml ./
RUN test ${RUN_POETRY_INSTALL_AT_BUILD_TIME} = "true" && poetry install || echo "skip to run poetry install."
RUN test ${RUN_POETRY_INSTALL_AT_BUILD_TIME} = "true" && mv ${APPLICATION_DIRECTORY}/.venv ${HOME}/.venv || echo "skip to move .venv."
