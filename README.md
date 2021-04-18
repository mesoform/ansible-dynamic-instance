# Cloud dynamic inventory creator #

Create dynamic Cloud inventory

## What is this repository for?

* provide a role to create Cloud instances and update the Ansible inventory
* Version 1

## Summary of set up
To be used as an [Ansible Galaxy role](https://docs.ansible.com/ansible/latest/galaxy/user_guide.html#installing-roles-from-galaxy)

### Configuration

The playbook is designed to run on a local machine. Either a developers laptop or some CICD build
agent and connect to whatever Docker Engine is defined by setting, or not, the environment variables
above.

However, when running on a Mac, it's been noticed that the correct version of Python isn't
identified. Returning errors like:
```yamlex
msg: 'Error: Docker SDK for Python version is 1.10.6 (gMacBookPro''s Python /usr/local/Cellar/ansible/2.10.5/libexec/bin/python3). Minimum version required is 2.1.0.Try pip uninstall docker-py followed by pip install docker.'
```
and
```yamlex
msg: 'Error connecting: Error while fetching server API version: HTTPSConnectionPool(host=''lab2'', port=2376): Max retries exceeded with url: /version (Caused by SSLError(SSLError(1, ''[SSL: TLSV1_ALERT_PROTOCOL_VERSION] tlsv1 alert protocol version (_ssl.c:1123)'')))'
```
on tasks that talk to Docker. I’ve resolved this previously by setting `ansible_python_interpreter
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


Change the preset values in `vars/main.yml` to meet your requirements and cloud service provider
settings. To allow for consistent names across CSPs there are some things to note:
* `workspace` = GCP project or AWS account
* `gcp_auth_kind` (in defaults) = setting for getting dynamic inventory to work due to poorly 
documented values and needing to investigate in the code. 
  

### Dependencies
You will need to have a predefined set of roles that you will use to configure the build instance 
before it is used to create a new image. The idea is to use git modules to manage this. To set this 
up, you must first create the submodule
```bash
URL_TO_ROLE_REPO=<URL_TO_ROLE_REPO> \
ROLE_NAME=<ROLE_NAME> \
REPO_ROOT=$(pwd) \
git submodule add \
--name $ROLE_NAME \
"$URL_TO_ROLE_REPO" \ 
$REPO_ROOT/roles/$ROLE_NAME
```
Change `<URL_TO_ROLE_REPO>` and `<ROLE_NAME>` for your own needs. `ROLE_NAME` is just an 
alphanumeric identifier 
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