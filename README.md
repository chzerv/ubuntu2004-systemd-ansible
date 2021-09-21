# Ubuntu 20.04 (Focal) Container Image for Ansible Testing

This Dockerfile builds an Ubuntu 20.04 (Focal) based container, capable of using `systemd`, mainly for Ansible role testing.

# How to build locally

1. Install [Docker](https://docs.docker.com/engine/install/).
2. Clone the repository: 

   ```shell
   git clone https://github.com/chzerv/ubuntu2004-systemd-ansible.git
   ```
3. `cd` into the directory and run `docker build -t ubuntu2004-systemd-ansible .`

> `docker` can be substituted for any other container engine, e.g., [Podman](https://podman.io/getting-started/installation.html).

# How to use

1. Install [Docker](https://docs.docker.com/engine/install/).

2. Pull this image from _Docker hub_:

    ```shell
    docker pull chzerv/ubuntu2004-systemd-ansible:latest
    ```
> If you built the image locally, you can use that instead.

Now, you can either run commands directly inside the container:

```shell
docker run -d --privileged --volume=/sys/fs/cgroup:/sys/fs/cgroup:ro ubuntu2004-systemd-ansible:latest ansible --version
```

Or, you can use it with [molecule](https://github.com/ansible-community/molecule):

```yaml
# molecule/default/molecule.yml
---
dependency:
  name: galaxy
driver:
  name: docker
lint: |
  set -e
  yamllint .
  ansible-lint
platforms:
  - name: instance
    image: "chzerv/${IMAGE:-ubuntu2004}-systemd-ansible:latest"
    command: ${DOCKER_COMMAND:-""}
    volumes:
      - /sys/fs/cgroup:/sys/fs/cgroup:ro
    privileged: true
    pre_build_image: true
provisioner:
  name: ansible
  playbooks:
    converge: "${MOLECULE_PLAYBOOK:-converge.yml}"
verifier:
  name: ansible
```

# Notes

This image is used for testing Ansible roles and playbooks locally and/or in CI, hence, security is not
a concern. It is not intended or recommended to use this image in production environments.

