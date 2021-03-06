# Introduction

This document will briefly detail the following:



*   Considerations and final scope of automation tools to be used.
*   Identified boundaries between ESXi initial deployment and post-deployment configuration.


# Automation Tools Considerations

I researched and considered the following automation tools:



*   Initial deployment:
    *   ESXi Kickstart (KS) answer file
    *   vSphere Auto Deploy
*   Post deployment
    *   Ansible (push based automation)
    *   Puppet (pull based automation)
    *   Chef (pull based automation)
    *   SaltStack (push and pull based automation)
    *   Terraform (push and pull based automation)


## ESXi Kickstart (KS) Answer File

Kickstart answer files are the simplest form of creating an unattended deployment of ESXi. It supports scripted installation on ISO, USB, and PXE. Kickstart files will be used for the Autobuilder project


## vSphere Auto Deploy

This solution requires more advanced infrastructure and configuration to allow “stateless” ESXi installations that minimizes configuration drift. Since this type of solution does have physical hardware implications, Auto Deploy will be revisited during DevStack BOM buildout. It falls out of scope at this time.


## Ansible

Ansible is a push based and agentless Infrastructure as Code (IaC) solution that you only install once. Ansible is a very powerful solution while only needing SSH and Python configured on the controller and targets for most modules. The solution also has excellent vendor and community support with detailed documentation. Ansible is best suited for configuration management and will be used for the Autobuilder project


## Terraform

Terraform is another agentless solution that leverages the Go programming language. If ansible is a configuration management tool, terraform can be seen as a provisioning tool for your environment/architecture. My research showed both of these tools can integrate well together and can invoke each other. Terraform will not be used here but will be considered during vCenter and Virtual Machine provisioning steps.


## Puppet, Chef, and SaltStack

All three of the solutions require installation on all machines. Puppet uses Puppet DSL as syntax, Chef uses Ruby as syntax, and SaltStack uses YAML syntax like Ansible. All three have a lot of overlap with Ansible and it did not seem like the vendor/community support and documentation was as comprehensive as Ansible. There are currently no plans to revisit these solutions later in the project.


# Initial and Post Deployment Boundary

Since the kickstart answer file comes with plenty of configuration options, a determination needed to be made on what the kickstart will configure during initial deployment, and what ansible will handle post-deployment. After discussions with team members, we decided that it would be most effective to configure values that are mostly static and rarely change, while allowing other configuration steps more flexibility to be changed dynamically using ansible.

The following will be core configurations inside of the kickstart file used during initial deployment:



*   Eula
*   Partition clearing
*   Setting root password (Known to Ansible controller)
*   Basic vSwitch network settings
*   Enable SSH (Ansible requirement)



NOTE: Ansible will be used to quickly generate the kickstart files using jinja2 templates. More advanced configuration, such as moving the auto-generated files to a TFTP server for PXE delivery is desired, but the DevStack BOM buildout will influence HOW this is done. The plan is to accomplish this in a future story once DevStack 2.0 is confirmed.
