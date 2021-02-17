# Ansible

copy a profile and then edit the profile to limit the amount of memory and the amount of cpu.
```
lxc profile copy default ansible 
lxc profile edit ansible 
``` 
Edit the profile and include the limits
```
config: 
  limits.cpu: "2"
  limits.memory: 2GB
```
Create two instance 
```
lxc launch ubuntu:20.04 -p ansible ansible-0
lxc launch ubuntu:20.04 -p ansible ansible-1
```
generate a ssh key, copy the public key to the lxc container 
```
ssh-keygen -t ed25519 -C "Adrian default"
lxc file push ~/.ssh/id_ed25519.pub ansible-0/home/ubuntu/
```
Connect to the lxc container with the ubuntu user and copy the content of the file to the authorized keys.
```
lxc exec ansible-0 su - ubuntu
cat id_ed25519.pub >> ~/.ssh/authorized_keys
```
know you can connect to the server `ssh ubuntu@ip`

You can create an agent to not type the passphrase each time `eval $(ssh-agent)` and after that `ssh-add` to make it easier we can create an alias with the follow command `alias ssha='eval $(ssh-agent) && ssh-add'` 

## Git

create a repository on GitHub and add the public key to GitHub `cat  ~/.ssh/id_ed25519.pub` 

## Installation

To install ansible 
```
sudo apt update
sudo apt install ansible
```
## Inventory file

create a file with the name `inventory` and include the IP addresses in this case 
```
10.13.92.172
10.13.92.223
```
then commit the change to git 

## First Test
Now is time to test how the configuration is going to test wi will use the command `ansible all --key-file ~/.ssh/ansible -i inventory -m ping -u ubuntu` let's review the command:
* **ansible all:** indicates to execute on all the inventory file list.
* **--key-file ~/.ssh/ansible:** indicates the private key to use in the connection.
* **-i inventory:** the inventory file
* **-m ping** indicates the module that we want to use.
* **-u ubuntu** to indicate the user.

The output should looks like:
```shell
10.13.92.172 | SUCCESS => {
    "ansible_facts": {
        "discovered_interpreter_python": "/usr/bin/python3"
    },
    "changed": false,
    "ping": "pong"
}
10.13.92.223 | SUCCESS => {
    "ansible_facts": {
        "discovered_interpreter_python": "/usr/bin/python3"
    },
    "changed": false,
    "ping": "pong"
}
``` 
Now is time to make a config file so create a file name `ansible.cfg` and set the values:
```conf
[defaults]
inventory = inventory
private_key_file = ~/.ssh/ansible
remote_user = ubuntu
```
This will override the `ansible.cfg` in the path `/etc/ansible` now the command can be shorted o `ansible all -m ping` lest review some commands

* Show a list of all servers
```
ansible all --list-hosts
hosts (2):
    10.13.92.172
    10.13.92.223
```
* This command show a lot of information about the servers, the limit argument is use to get the information from 1 server. 
```
ansible all -m gather_facts
ansible all -m gather_facts --limit 10.13.92.172
```

