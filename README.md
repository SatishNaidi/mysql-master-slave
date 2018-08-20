# mysql-master-slave-replication
Docker setup for MySQL Master Slave replication 

### Edit the hosts file with list of servers ###

###  Change the ansible.cfg file to appropriate private key file and remote user ###

```
[defaults]
host_key_checking=False
private_key_file=/Users/naisa13/mykeys/Mumbai.pem
inventory=hosts
remote_user=ec2-user
```

## To Run the Playbook ##
```
ansible-playbook install_docker.yml  -vvvv
```
