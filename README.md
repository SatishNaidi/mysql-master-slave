# mysql-master-slave-replication
Docker setup for MySQL Master Slave replication 

#### Clone the repository to Ansible server using following command ####
```
git clone https://github.com/SatishNaidi/mysql-master-slave.git
```
#### Change the working directory ####
```
cd mysql-master-slave
```
### Edit the hosts file with list of servers ###
```
[mysql]
server1
server2
server3
```

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
ansible-playbook install_mysql_master_slave.yml  -vvvv
```
## To check the replication status ## 

```
docker exec mysql_master sh -c "export MYSQL_PWD=MyPassword12; mysql -u root mydb -e 'SHOW MASTER STATUS\G'"
```
```
docker exec mysql_slave sh -c "export MYSQL_PWD=MyPWD123; mysql -u root mydb -e 'SHOW SLAVE STATUS\G'"
```

### Create a table in Master and Insert sample data ###

```
docker exec mysql_master sh -c "export MYSQL_PWD=MyPassword12; mysql -u root mydb -e 'create table table1(num int); insert into table values (12), (13)'"
```

### Check the replication on Slave ###

```
docker exec mysql_slave sh -c "export MYSQL_PWD=MyPWD123; mysql -u root mydb -e 'select * from table1 \G'"
```

#### Additional help in cleaning up dockers ####
https://zaiste.net/removing_docker_containers

### Command Line way of installing docker-compose ###
curl -L https://github.com/docker/compose/releases/download/1.16.1/docker-compose-`uname -s`-`uname -m` > ./docker-compose
mv ./docker-compose /usr/bin/docker-compose
chmod +x /usr/bin/docker-compose/
