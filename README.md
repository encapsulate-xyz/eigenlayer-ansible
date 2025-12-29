# Ansible Playbook to deploy Eigenlayer Services

This Ansible playbook automates the deployment and configuration of eigenlayer services. It ensures that the necessary dependencies, configuration files, and services are properly set up and running.

## Table of Contents

- [Requirements](#requirements)
- [Prerequisites](#prerequisites)
- [Setup](#setup)
- [Variables](#variables)
- [Usage](#usage)

## Requirements

Before using this playbook, ensure the following requirements are met:

1. **Ansible version**: Make sure you have Ansible 2.15+ installed.
2. **SSH Access**: Passwordless SSH access to all target servers.
3. **Python**: Python 3.x installed on the control node and all target hosts.
4. **Privileges**: The user running the playbook must have sudo privileges on the target machines.

## Prerequisites

**Install HashiCorp Vault**

This playbook relies on HashiCorp Vault to securely retrieve sensitive files, such as validator and node keys. Follow the [HashiCorp Vault Installation Guide](https://developer.hashicorp.com/vault/tutorials/getting-started/getting-started-install) to set up Vault on your infrastructure.

**Note on Secrets Management**

The playbook dynamically retrieves secret environment file from HashiCorp Vault. The keys are expected to follow a structured path format:
`<environment>/<project>/<organization>/<type>/<file_name>`

For example:
- [`eigenlayer/encapsulate/eigenda/eigenlayer-eigenda.secrets.env`](roles/eigenda/templates/secrets.env.example)

This structure ensures easy organization and secure retrieval of secrets.

## Setup

### 1. Install Ansible

If Ansible is not installed, visit the official documentation for detailed instructions on how to install Ansible on various Linux distributions:

[Ansible Installation Guide](https://docs.ansible.com/ansible/latest/installation_guide/installation_distros.html)

### 2. Clone the repository

Clone this repository to your Ansible control node:

```bash
git clone https://github.com/encapsulate-xyz/eigenlayer-ansible.git
cd eigenlayer-ansible
```

### 3. Inventory

Define your target servers' IP address or DNS in the inventory folder, and select either `mainnet.yml` or `testnet.yml` to update.

Example for testnet.yml

```yaml
---
# maintains testnet inventory grouped by project names
all:
  vars:
    env: testnet
  children:
    eigenlayer:
      hosts:
        eigenda.eigen.testnet.encapsulate.xyz:
          type: eigenda
        eoracle.eigen.testnet.encapsulate.xyz:
          type: eoracle

```

## Variables

This playbook allows customization through several variables. You can define these variables in the following locations:

- **`group_vars/all.yml`**: Contains all the source urls and configurations.
- **`group_vars/mainnet.yml`** or **`group_vars/testnet.yml`**: Contains version-specific variables.
- **`group_vars/vault.yml`**: Store secret variables, such as `jwtsecret`, in this file.

### Usage

1. First, install the dependencies:

  ```bash
   ansible-galaxy install -r requirements.yml
  ```

2. Create a `ansible_vault_password` file containing ansible-vault password

3. Then run the playbook:

  ```bash
  ansible-playbook setup_eigenda.yml -l eigenda.eigen.testnet.encapsulate.xyz
  ```

After you run the playbook, it will ask for confirmation, displaying all the variables and the IP address or DNS of the server you are going to deploy.

Example output:

```bash
TASK [Display environment being deployed to] ***************************************************************************************************
ok: [eigenda.eigen.testnet.encapsulate.xyz] => {
    "msg": [
        "Deploying to Host: eigenda.eigen.testnet.encapsulate.xyz",
        "Groups: ['eigenlayer']",
        "Project: eigenlayer",
        "Environment: testnet",
        "Type: eigenda",
        "Version: 2.2.0",
        "Username: eigenlayer",
        "Service Name: eigenlayer",
        "Operating System: linux",
        "System Architecture: amd64"
    ]
}

TASK [Confirm deployment details] ********************************************************************************************************************
Pausing for 40 seconds
(ctrl+C then 'C' = continue early, ctrl+C then 'A' = abort)
ok: [eigenda.eigen.testnet.encapsulate.xyz]

TASK [Please confirm again] ********************************************************************************************************************
ok: [eigenda.eigen.testnet.encapsulate.xyz] => {
    "msg": [
        "Deploying to Host: eigenda.eigen.testnet.encapsulate.xyz",
        "Project: eigenlayer",
        "Environment: testnet",
        "Type: eigenda"
    ]
}

TASK [Confirm deployment details] **************************************************************************************************************
Pausing for 20 seconds
(ctrl+C then 'C' = continue early, ctrl+C then 'A' = abort)
ok: [eigenda.eigen.testnet.encapsulate.xyz]
```
