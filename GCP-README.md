# Selfhosted GitLab-instance and runners setup for Google Cloud Platform

## Prerequisites

- **Debian-based OS on the target machine**.
- **Hostname pointed to your target machine with GitLab instance**.  
_This is required for correct runners setup and also for basic email services to work - registration of users, password resets etc_.


## Setup

1. Edit the `vars/main.yml`:

   * Assign name of your project to `gcp_project`
   * Generate **Service Account auth file**:  
     - Go to the hamburger menu in the top left corner to open the main navigation menu
     - Navigate to **IAM & Admin** -> **Service Accounts**
     - Click on default Service Account in the list
     - On Service Account page select **Keys**
     - Click on **ADD KEY** -> **Create new key**
     - Select **JSON** and click **CREATE*
     - The browser will download auth file automatically
     - Rename file to `service.json` and move it to `secure/service.json`
   * Assign service account email to `service_account_email`
     * It's available in service account auth file under `client_email`
   * Assign hostname associated with your GitLab instance IP to `gitlab_hostname`.
   * Assign existing root password (or the one you want to create GitLab instance with) to `gitlab_root_password`
   * Assign ghcr.io link to GitHub account to `gitlab_runner_allowed_images`
     * Example `ghcr.io/jetbrains-academy/python-cub/*`  


2. _(optional)_ Reserver IP addresses for GitLab instance and/or GitLab runners server:
   * Go to the hamburger menu in the top left corner to open the main navigation menu
   * Navigate to **VPC network** -> **IP addresses**
   * Click on **RESERVE EXTERNAL STATIC IP ADDRESS**
   * Give it appropriate name and description
   * Select **Network Service Tier** - **Standard**
   * Select **IP version** - **IPv4**
   * Select **Type** - **Regional** and set **Region** to the region you'll use for your servers
     * By default, this config uses region `europe-west1` (Belgium) when creating instance
   * Assign reserved IPs to `gitlab_server_ip` and `gitlab_runner_server_ip` in `vars/main.yml` and uncomment them

## Installation

### Full rollout

Full roll-out includes:
1) Creating VM for GitLab instance
2) Creating VM for GilLab runners
3) Installing GitLab, including mail server and SSL certificate for the hostname
4) Installing GitLab shared runner on VM, registering it with GitLab instance

Run the playbook
```shell
ansible-playbook -i inventory.ini playbooks/gitlab.yml --skip-tags "logging"
```

Remove `--skip-logging "logging"` to download GitLab installation log, GitLab config file and printing GitLab runner token during execution.

### Only instantiating VMs

This option will only instantiate either one or both VMs, without installing GitLab or Runner.  
You can than install GitLab and runner via dedicated playbooks described in [main readme file](README.md)

Run the playbook
```shell
ansible-playbook -i inventory.ini playbooks/gitlab.yml --tags "gitlab-server-vm" --skip-tags "logging"
```
or
```shell
ansible-playbook -i inventory.ini playbooks/gitlab.yml --tags "gitlab-runners-vm" --skip-tags "logging"
```

The default values like number of CPU, RAM, disk size etc. are available in [this file](roles/create_gcp_instance/defaults/main.yml).  
They also can be edited in the [playbook](playbooks/GCP_for_gitlab.yml).
Default zone for the VM is Europe, Belgium (`europe-west1-b`)

### Instantiate VM and install GitLab/runners separately

This option will instantiate VM and install GitLab or runners separately.

Run the playbook
```shell
ansible-playbook -i inventory.ini playbooks/gitlab.yml --tags "gitlab-install" --skip-tags "logging"
```
or
```shell
ansible-playbook -i inventory.ini playbooks/gitlab.yml --tags "gitlab-runners-install" --skip-tags "logging"
```