# Azure VM SIEM Setup Guide

Welcome to the **Azure VM SIEM Setup Guide**, a comprehensive resource for deploying a Security Information and Event Management (SIEM) solution on an Azure Virtual Machine. This guide is intended for security teams, system administrators, and IT professionals who need advanced security monitoring and incident management capabilities in Azure.

--------------------------------------------------------------------------------

## Table of Contents

- [Introduction](#introduction)
- [Architecture Overview](#architecture-overview)
- [Prerequisites](#prerequisites)
- [Step-by-Step Guide](#step-by-step-guide)
  - [Provisioning the Azure VM](#provisioning-the-azure-vm)
  - [Installing SIEM Software](#installing-siem-software)
  - [Configuring the SIEM](#configuring-the-siem)
  - [Post-Installation Steps](#post-installation-steps)
- [Automation Scripts](#automation-scripts)
- [Best Practices & Troubleshooting](#best-practices--troubleshooting)
- [Further Resources](#further-resources)
- [Contributing](#contributing)
- [License](#license)
- [Contact](#contact)

--------------------------------------------------------------------------------

## Introduction

Effective security monitoring is essential for protecting organizational systems and data. A SIEM solution aggregates logs and events from multiple sources, providing actionable insights for rapid threat detection.

This guide covers:
- Provisioning an Azure Virtual Machine for SIEM operations.
- Installing Wazuh alongside the Elastic Stack (ELK).
- Configuring and maintaining your SIEM environment.

--------------------------------------------------------------------------------

## Architecture Overview

**Key components:**

- **Azure Virtual Machine (VM):**
  Hosts the SIEM software, provisioned with sufficient CPU, RAM, and storage.

- **SIEM Software:**
  - **Wazuh:** An open-source platform for security monitoring and threat detection.
  - **Elastic Stack (ELK):** Elasticsearch, Logstash, and Kibana for log aggregation, processing, and visualization.

- **Networking & Security:**
  - Azure Network Security Groups (NSGs) to control inbound/outbound access.
  - TLS encryption for secure communication.

- **Automation:**
  - Azure CLI and Bash scripts to streamline the entire deployment.

--------------------------------------------------------------------------------

## Prerequisites

Before proceeding, ensure you have:

- An **active Azure subscription** with permissions to create and manage resources.
- **Basic Azure knowledge** (Portal or CLI).
- **Linux command-line experience**.
- **SIEM fundamentals**, including log collection and correlation.

**Software Requirements:**
- Azure CLI
- SSH client
- Text editor (e.g., VSCode, Vim)

--------------------------------------------------------------------------------

## Step-by-Step Guide

### Provisioning the Azure VM

1. **Log in to Azure:**
   Use the command `az login` to authenticate with your Azure account.

2. **Define VM Configuration:**
   - VM Size: Standard DS3 v2 (4 vCPUs, 14 GB RAM)
   - Region: Close to your data sources
   - Image: Ubuntu 20.04 LTS or later

3. **Create Resource Group:**
   Example:
   `az group create --name siem-rg --location eastus`

4. **Provision VM:**
   Replace `<your-ssh-key-path>` with your SSH public key path:
az vm create
--resource-group siem-rg
--name siem-vm
--image UbuntuLTS
--size Standard_DS3_v2
--admin-username azureuser
--ssh-key-value <your-ssh-key-path>
--public-ip-address-dns-name siemvm-unique
--output table

java
Copy
Edit

5. **Configure Networking:**
Example commands for allowing SSH (port 22) and Kibana (port 5601):
az network nsg rule create --resource-group siem-rg --nsg-name siem-vmNSG --name AllowSSH --protocol tcp --priority 1000 --destination-port-range 22 --access Allow az network nsg rule create --resource-group siem-rg --nsg-name siem-vmNSG --name AllowKibana --protocol tcp --priority 1010 --destination-port-range 5601 --access Allow

markdown
Copy
Edit

### Installing SIEM Software

1. **Connect via SSH:**
ssh azureuser@siemvm-unique.eastus.cloudapp.azure.com

markdown
Copy
Edit

2. **Update System:**
sudo apt update && sudo apt upgrade -y

markdown
Copy
Edit

3. **Install Dependencies:**
sudo apt install wget curl apt-transport-https -y

markdown
Copy
Edit

4. **Install Wazuh:**
curl -s https://packages.wazuh.com/key/GPG-KEY-WAZUH | sudo apt-key add - echo "deb https://packages.wazuh.com/4.x/apt/ stable main" | sudo tee /etc/apt/sources.list.d/wazuh.list sudo apt update && sudo apt install wazuh-manager -y

markdown
Copy
Edit

5. **Install the ELK Stack:**
- **Elasticsearch:**
  ```
  wget -qO - https://artifacts.elastic.co/GPG-KEY-elasticsearch | sudo apt-key add -
  sudo apt-add-repository "deb https://artifacts.elastic.co/packages/7.x/apt stable main"
  sudo apt update && sudo apt install elasticsearch -y
  sudo systemctl enable --now elasticsearch
  ```
- **Kibana:**
  ```
  sudo apt install kibana -y
  sudo systemctl enable --now kibana
  ```
- **Logstash:**
  ```
  sudo apt install logstash -y
  sudo systemctl enable --now logstash
  ```

### Configuring the SIEM

- **Customize Logstash Pipelines:**
Tailor pipelines to your log sources and normalization needs.

- **Elasticsearch Tuning:**
Modify index settings, shard allocation, and retention policies.

- **Security Enhancements:**
Enable TLS/SSL and configure additional firewall rules (e.g., UFW).

- **Testing:**
Generate test logs and check Kibana dashboards for proper data flow.

### Post-Installation Steps

1. **Reboot & Verify:**
sudo reboot

markdown
Copy
Edit
After reboot, confirm:
- Elasticsearch is running on the correct port.
- Kibana is accessible on port 5601.
- Logstash is processing logs.
- Wazuh manager is generating alerts for test events.

2. **Access Kibana:**
Visit `http://<your-vm-public-ip>:5601` to set up Kibana.

3. **Continuous Monitoring:**
Schedule regular health checks and resource monitoring to maintain performance.

--------------------------------------------------------------------------------

## Automation Scripts

Example Azure CLI script (`scripts/create-vm.sh`):
#!/bin/bash az group create --name siem-rg --location eastus az vm create --resource-group siem-rg --name siem-vm --image UbuntuLTS --size Standard_DS3_v2
--admin-username azureuser --ssh-key-value ~/.ssh/id_rsa.pub --public-ip-address-dns-name siemvm-unique

markdown
Copy
Edit
Scripts are modular and adaptable for different environments.

--------------------------------------------------------------------------------

## Best Practices & Troubleshooting

- **Regular Updates** for OS and SIEM software.
- **Automated Backups** via Azure Backup or other solutions.
- **Resource Monitoring** for CPU, memory, and disk usage.
- **Security Audits** and multi-factor authentication for sensitive areas.

**Troubleshooting:**
- Check service logs in `/var/log/`.
- Verify NSG rules for correct port configurations.
- Adjust Elasticsearch shard settings if performance is sluggish.

--------------------------------------------------------------------------------

## Further Resources

- Azure Documentation: https://docs.microsoft.com/azure/
- Wazuh Documentation: https://documentation.wazuh.com/
- Elastic Stack Guides: https://www.elastic.co/guide/index.html

--------------------------------------------------------------------------------

## Contributing

1. **Fork and clone** this repository.
2. **Discuss changes** in an issue if significant.
3. **Submit a pull request** with clear details and rationale.

--------------------------------------------------------------------------------
Thank you for using the **Azure VM SIEM Setup Guide**!
