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

create a repository on GitHub and add the public key to GitHub `cat  ~/.ssh/id_ed25519.pub` 