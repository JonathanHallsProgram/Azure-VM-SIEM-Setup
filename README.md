# Azure-VM-SIEM-Setup

## Table of Contents

1. [Introduction](#introduction)
2. [Prerequisites](#prerequisites)
3. [Setup Guide](#setup-guide)
   - [Creating an Azure Virtual Machine with RDP Access](#creating-an-azure-virtual-machine-with-rdp-access)
   - [Setting up the SIEM Solution](#setting-up-the-siem-solution)
   - [Creating and Adding Playbooks](#creating-and-adding-playbooks)
   - [Verifying the Setup](#verifying-the-setup)
4. [Files in This Repository](#files-in-this-repository)
5. [Conclusion](#conclusion)
6. [License](#license)
7. [Contributors](#contributors)

## Introduction

This repository explains how I set up an Azure Virtual Machine (VM), configured Remote Desktop Protocol (RDP) access, installed a Security Information and Event Management (SIEM) solution within the VM, and added multiple playbooks for automated response scenarios.

## Prerequisites

Before getting started, ensure you have the following:

- An active Azure subscription.
- Basic knowledge of Azure Portal and PowerShell/CLI commands.
- Access to RDP client (e.g., Remote Desktop on Windows).
- Familiarity with SIEM systems (Azure Sentinel or Elastic SIEM).
- Azure Playbooks (Logic Apps) for automation.

## Setup Guide

### Creating an Azure Virtual Machine with RDP Access

To begin, I logged in to the [Azure Portal](https://portal.azure.com) and created a new virtual machine. I went to **Virtual Machines** and clicked **Create**. I filled in the necessary details, such as selecting my Azure subscription, creating a resource group, and naming the VM (`SIEM-VM`). I chose **Windows Server 2019 Datacenter** as the image and selected an appropriate size for the workload.

Next, I set up the **Administrator Account** by creating a username and password. I also enabled **RDP (3389)** under **Inbound Ports** to allow remote access.

After reviewing the configuration, I clicked **Create** and waited for the VM to deploy. Once deployment was complete, I connected to the VM using **RDP** by downloading the RDP file from the Azure Portal and logging in with the credentials I had set up.

### Setting up the SIEM Solution

Once I had access to the VM, I opened the **Azure Portal** within the VM and installed **Azure Sentinel**. I created a Sentinel workspace and attached it to a new **Log Analytics workspace**.

To start ingesting data, I connected various data sources through the **Data connectors** feature in the Sentinel dashboard. These sources included **Azure Activity**, **Office 365**, and **Microsoft Defender for Cloud**. I followed the setup wizard to enable data ingestion from these sources.

For those interested in using **Elastic SIEM** instead of Azure Sentinel, I would recommend downloading the Elastic Stack on the VM, installing the Elastic Agent, and setting up Kibana and Elasticsearch to visualize and manage the logs.

### Creating and Adding Playbooks

To automate responses, I created **Logic Apps** in the Azure Portal, which are used as playbooks in Azure Sentinel. I went to **Logic Apps**, clicked **Create**, and chose the same resource group as my Sentinel. I named the Logic App `Incident-Response-Playbook`.

Using the **Designer**, I created a workflow with a trigger (e.g., when an incident is created in Sentinel) and actions such as sending an email, blocking an IP address, or notifying via Microsoft Teams. I saved the Logic App and then attached it to Sentinel through the **Playbooks** section.

I repeated this process to create additional playbooks for various scenarios, such as automatically isolating a compromised host, responding to phishing attempts, and suspending user accounts if suspicious activity was detected.

### Verifying the Setup

To verify everything was working correctly, I tested **RDP access** by logging in to the VM from my local machine. I also checked the **SIEM data ingestion** by viewing the live data in the Sentinel dashboard.

Finally, I simulated security incidents to ensure that the playbooks were triggered and that the actions were executed as expected.

## Files in This Repository

- `README.md`: This guide for setting up Azure VM, SIEM, and playbooks.
- `Playbooks/`: Example playbooks for automated response workflows.
- `Scripts/`: Optional PowerShell scripts for automating some VM and SIEM setup steps.

## Conclusion

This repository provides a detailed approach to how I set up an Azure Virtual Machine with RDP access, configured a SIEM system (Azure Sentinel), and integrated multiple playbooks for automating incident responses. Follow the instructions carefully to ensure everything is configured correctly.

Feel free to modify the playbooks or enhance the SIEM setup based on your specific security needs.

## License

This project is licensed under the MIT License.

## Contributors

- Jonathan Halls - Initial Setup
