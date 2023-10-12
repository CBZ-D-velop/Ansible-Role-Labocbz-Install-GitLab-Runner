# Ansible role: labocbz.install_gitlab_runner

![Licence Status](https://img.shields.io/badge/licence-MIT-brightgreen)
![CI Status](https://img.shields.io/badge/CI-success-brightgreen)
![Testing Method](https://img.shields.io/badge/Testing%20Method-Ansible%20Molecule-blueviolet)
![Testing Driver](https://img.shields.io/badge/Testing%20Driver-docker-blueviolet)
![Language Status](https://img.shields.io/badge/language-Ansible-red)
![Compagny](https://img.shields.io/badge/Compagny-Labo--CBZ-blue)
![Author](https://img.shields.io/badge/Author-Lord%20Robin%20Crombez-blue)

## Description

![Tag: Ansible](https://img.shields.io/badge/Tech-Ansible-orange)
![Tag: Debian](https://img.shields.io/badge/Tech-Debian-orange)
![Tag: GitLab](https://img.shields.io/badge/Tech-GitLab-orange)
![Tag: Runners](https://img.shields.io/badge/Tech-Runners-orange)
![Tag: CI/CD](https://img.shields.io/badge/Tech-CI%2FCD-orange)

An Ansible role to install and configure a GitLab runner on your host, with Docker

This Ansible role is designed to automate the deployment of a GitLab runner in a Debian environment using Docker for CI/CD purposes. It provides flexibility to configure various aspects of the GitLab runner, such as the number of concurrent jobs, the check interval, and the session duration. The runner is associated with the specified GitLab instance using a URL and an access token. These settings can be adjusted to suit specific project requirements.

Additionally, the role configures Docker for the runner, with options like TLS verification, the base image, privileged access, and Docker cache management, allowing for complete customization based on user preferences.

Ultimately, this role simplifies the deployment of a GitLab runner in a Debian environment with Docker while offering the flexibility needed to tailor the configuration to each project's needs.

## Folder structure

By default Ansible will look in each directory within a role for a main.yml file for relevant content (also man.yml and main):

```PYTHON
.
├── README.md  # Contains an overview of the role and its purpose.
├── defaults
│   ├── main.yml  # Contains default variables for the role that can be overridden by users.
│   └── README.md  # Contains documentation for the default variables.
├── files
│   └── README.md  # Contains documentation for the files in the directory.
├── handlers
│   ├── main.yml  # Contains handlers that can be called by tasks within the role.
│   └── README.md  # Contains documentation for the handlers.
├── meta
│   ├── main.yml  # Contains metadata about the role, including dependencies and supported platforms.
│   └── README.md  # Contains documentation for the role metadata.
├── tasks
│   ├── main.yml  # Contains tasks to be executed by the role on the managed nodes.
│   └── README.md  # Contains documentation for the tasks.
├── templates
│   └── README.md  # Contains documentation for the templates.
└── vars
    ├── main.yml  # Contains variables that are specific to the role and are not meant to be overridden.
    └── README.md  # Contains documentation for the role variables.
```

## Tests and simulations

### Basics

You have to run multiples tests. *tests with an # are mandatory*

```MARKDOWN
# lint
# syntax
# converge
# idempotence
# verify
side_effect
```

Executing theses test in this order is called a "scenario" and Molecule can handle them.

Molecule use Ansible and pre configured playbook to create containers, prepare them, converge (run the role) and verify its execution.
You can manage multiples scenario with multiples tests in order to get a 100% code coverage.

This role contains a ./tests folder. In this folder you can use the inventory or the tower folder to create a simualtion of a real inventory and a real AWX / Tower job execution.

### Command reminder

```SHELL
# Check your YAML syntax
yamllint -c ./.yamllint .

# Check your Ansible syntax and code security
ansible-lint --config=./.ansible-lint .

# Execute and test your role
molecule lint
molecule create
molecule list
molecule converge
molecule verify
molecule destroy

# Execute all previous task in one single command
molecule test
```

## Installation

To install this role, just copy/import this role or raw file into your fresh playbook repository or call it with the "include_role/import_role" module.

## Usage

### Vars

Some vars a required to run this role:

```YAML
---
install_gitlab_runner_concurrent: 5
install_gitlab_runner_session_timeout: 1800
install_gitlab_runner_image: "gitlab/gitlab-runner:latest"
install_gitlab_runner_name: "gitlab-runners"
install_gitlab_runner_url: "https://gitlab.com/"
install_gitlab_runner_token: "XXXXXXX"

install_gitlab_runner_config_volume: "{{ install_gitlab_runner_name }}"
install_gitlab_runner_docker_image: "debian:11"


```

The best way is to modify these vars by copy the ./default/main.yml file into the ./vars and edit with your personnals requirements.

You can set vars in the template model in Ansible AWX / Tower or just surchage them during the playbook call.

In order to surchage vars, you have multiples possibilities but for mains cases you have to put vars in your inventory and/or on your AWX / Tower interface.

```YAML
# From inventory
---
inv_install_gitlab_runner_concurrent: 5
inv_install_gitlab_runner_session_timeout: 1800
inv_install_gitlab_runner_image: "gitlab/gitlab-runner:latest"
inv_install_gitlab_runner_name: "gitlab-runners"
inv_install_gitlab_runner_url: "https://gitlab.com/"
inv_install_gitlab_runner_token: "XXXXXXX"

inv_install_gitlab_runner_config_volume: "{{ inv_install_gitlab_runner_name }}"
inv_install_gitlab_runner_docker_image: "debian:11"


```

```YAML
# From AWX / Tower
---

```

### Run

To run this role, you can copy the molecule/default/converge.yml playbook and add it into your playbook:

```YAML
- name: "Include labocbz.install_gitlab_runner"
  tags:
    - "labocbz.install_gitlab_runner"
  vars:
    install_gitlab_runner_concurrent: "{{ inv_install_gitlab_runner_concurrent }}"
    install_gitlab_runner_session_timeout: "{{ inv_install_gitlab_runner_session_timeout }}"
    install_gitlab_runner_image: "{{ inv_install_gitlab_runner_image }}"
    install_gitlab_runner_name: "{{ inv_install_gitlab_runner_name }}"
    install_gitlab_runner_url: "{{ inv_install_gitlab_runner_url }}"
    install_gitlab_runner_token: "{{ inv_install_gitlab_runner_token }}"
    install_gitlab_runner_config_volume: "{{ inv_install_gitlab_runner_config_volume }}"
    install_gitlab_runner_docker_tls_verify: "{{ inv_install_gitlab_runner_docker_tls_verify }}"
    install_gitlab_runner_docker_image: "{{ inv_install_gitlab_runner_docker_image }}"
  ansible.builtin.include_role:
    name: "labocbz.install_gitlab_runner"

```

After the run and the registration, you can modify the config.toml file and add some specific configuration. For example this is some configuration to make the runner able to do Docker in Docker.

```TOML
  [runners.cache]
    MaxUploadedArchiveSize = 0
  [runners.docker]
    tls_verify = false
    image = "alpine:3.18"
    privileged = true
    disable_entrypoint_overwrite = false
    oom_kill_disable = false
    disable_cache = false
    pull_policy = ["always","if-not-present"]
    shm_size = 0
    volumes = ["/cache", "/var/run/docker.sock:/var/run/docker.sock", "/sys/fs/cgroup:/sys/fs/cgroup:rw", "/var/lib/containerd"]
    network_mode = "host"
  [runners.docker.services_tmpfs]
    "/run" = "rw"
    "/run/lock" = "rw"
    "/tmp" = "rw"
```

## Architectural Decisions Records

Here you can put your change to keep a trace of your work and decisions.

### 2023-09-10: First Init

* First init of this role with the bootstrap_role playbook by Lord Robin Crombez
* Role create a volume and import a config.toml file for the runner configuration
* Its possible de deploy multiples runners, if theirs names are differents (call the role multiple times)
* You have to provide an access token to the role and an URL for GitLab
* TLS verification can be removed, so you can use your GitLab instance on premise

### 2023-09-28: Registration

* Registration is done by command
* No config.toml because GitLab have changed their process

## Authors

* Lord Robin Crombez

## Sources

* [Ansible role documentation](https://docs.ansible.com/ansible/latest/playbook_guide/playbooks_reuse_roles.html)
* [Ansible Molecule documentation](https://molecule.readthedocs.io/)
* [Run GitLab Runner in a container](https://docs.gitlab.com/runner/install/docker.html)
* [Docker executor](https://docs.gitlab.com/runner/executors/docker.html)
* [gitlab-runner register should allow to define concurrent value](https://gitlab.com/gitlab-org/gitlab-runner/-/issues/1539)
