# USING ANSIBLE SETUP MONITORING

Ansible is an open source automation platform. It is very, very simple to setup and yet powerful. 
Ansible can help you with configuration management, application deployment, task automation. It can also do IT orchestration, where you have to run tasks in sequence and create a chain of events which must happen on several different servers or devices

Ansible playbook
================

An Ansible playbook is an organized unit of scripts that defines work for a server configuration managed by the automation tool Ansible. Ansible is a configuration management tool that automates the configuration of multiple servers by the use of Ansible playbooks. ... Ansible plays are written in YAML.

```bash
   ansible-playbook -i inventory.ini sshd-monitor.yml --tags all
```   
