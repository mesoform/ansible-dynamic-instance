# Cloud dynamic inventory creator #

Create dynamic Cloud inventory

## What is this repository for?

* provide a role to create Cloud instances and update the Ansible inventory
* Version 1

## Summary of set up
To be used as an [Ansible Galaxy role](https://docs.ansible.com/ansible/latest/galaxy/user_guide.html#installing-roles-from-galaxy)

### Configuration

The playbook is designed to run on against a local machine where the relevant libraries are installed for the given Cloud provider. It will run
against localhost because the point of this playbook is to dynamically create the instances which we're then going to configure. It will then update
the Ansible inventory.

roles/requirements.yml
```yamlex
- src: https://github.com/mesoform/ansible-dynamic-instance.git
  name: create-nodes
  scm: git
```

Root playbook
```yamlex
- name: create build instance
  hosts: localhost
  gather_facts: no
  connection: local
  pre_tasks:
    - name: Verify Ansible version
      assert:
        that: "ansible_version.full is version_compare('2.7', '>=')"
        mst: "Ansible version must be at least version 2.7"
  roles:
    - role: create-nodes
      tags:
        - create_instance
```

However, when running on a Mac, it's been noticed that the correct version of Python isn't
identified. Returning errors like:
```yamlex
msg: 'Error: SDK for Python version is 1.10.6 (gMacBookPro''s Python /usr/local/Cellar/ansible/2.10.5/libexec/bin/python3). Minimum version required is 2.1.0.Try pip uninstall docker-py followed by pip install docker.'
```
and
```yamlex
msg: 'Error connecting: Error while fetching server API version: HTTPSConnectionPool(host=''lab2'', port=2376): Max retries exceeded with url: /version (Caused by SSLError(SSLError(1, ''[SSL: TLSV1_ALERT_PROTOCOL_VERSION] tlsv1 alert protocol version (_ssl.c:1123)'')))'
```
If you bump into such an issue, I’ve resolved this previously by setting `ansible_python_interpreter
= /usr/bin/python3` in `ansible.cfg`. Recently though, this has stopped working and required. Use
one of the following options (change the path to the correct location)

in `main.yml`, add:
```yamlex
- hosts: localhost
  vars:
    ansible_python_interpreter: /usr/local/bin/python3

```
in `/etc/ansible/hosts` add
```ini
[localhosts]
localhost ansible_python_interpreter=/usr/local/bin/python3 ansible_connection=local
```
On the command line add:
```shell
ansible-playbook -v main.yml -e 'ansible_python_interpreter=/usr/local/bin/python3'
```
See the [Ansible documentation](https://docs.ansible.com/ansible/latest/reference_appendices/python_3_support.html)


Change the preset values by creating your own `vars/main.yml` to meet your requirements and cloud service provider
settings. To allow for consistent names across CSPs there are some things to note:
* `workspace` = GCP project or AWS account
* `gcp_auth_kind` (in defaults) = setting for getting dynamic inventory to work due to poorly 
documented values and needing to investigate in the code.


### How to run tests
* 
### Deployment instructions
*

## Contribution guidelines

* Writing tests
* Code review
* Other guidelines

## Who do I talk to?

* Repo owner or admin
* Other community or team contact
