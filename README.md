# Manytask infrastructure setup with Ansible


## Overview

This repository contains Ansible playbooks to setup a Manytask infrastructure.  
All, one or several of the following services can be installed:
* **GitLab server** - setup self-hosted GitLab instance \w docker registry, etc.
* **Web server**    - setup server to host the Manytask web application in a docker.
* **GitLab Runner** - setup self-hosted GitLab CI runner(s) to run CI/CD jobs from self-hosted GitLab server (for students' jobs).
* **GitHub Runner** - setup self-hosted GitHub Actions runner(s) to run CI/CD jobs from github.com (for private repos).

Note: GitLab and GitHub runners can be installed on the same server. 


## Prerequisites

- Debian-based OS on target machine (for GitLab instance and its runners).
- Ansible installed on your control machine.
  ```shell
  # Optional: Create a virtual environment
  python -m venv .venv
  source .venv/bin/activate
  ```
  ```shell
  # Install Ansible
  python -m pip install -r requirements.txt
  ```
- Load all ansible roles required for the playbooks:
  ```shell
  ansible-galaxy install -r requirements.yml
  ```
- SSH access to all the servers you want to manage with Sudo privileges.

## Setup

1. Edit the `inventory.ini` file to match your infrastructure.
2. Edit the `vars/main.yml` file to match your infrastructure.
  * For github runner, you need to provide a github-runner registration token. You can find it in your github repository settings.
    Repo -> Settings -> Actions -> Runners -> New self-hosted runner -> Copy the registration token.
  * For GitLab runner, you need to provide password for root account.
  * You need hostname associated with your GitLab instance IP.
3. Run the playbooks you want:
  ```shell
  ansible-playbook -i inventory.ini playbooks/gitlab.yml
  ansible-playbook -i inventory.ini playbooks/web.yml
  ansible-playbook -i inventory.ini playbooks/github_runner.yml
  ansible-playbook -i inventory.ini playbooks/gitlab_runner.yml
  ```
5. If you want to use Google Cloud Platform for installation, you need to follow instruction in [GCP guide](GCP-README.md).


## Development

1. Install Ansible in a virtual environment:
  ```shell
  python -m venv .venv
  source .venv/bin/activate
  python -m pip install -r requirements.txt
  ```

2. Make changes to the playbooks.

3. Validate playbooks syntax:
  ```shell
  ansible-playbook --syntax-check -i inventory.ini playbooks/TODO.yml
  ``` 

4. Lint playbooks:
  ```shell
  yamllint .
  ansible-lint
  ```

5. Make PR - the github actions pipeline will be executed automatically to check linting, run playbooks and check idempotency.
