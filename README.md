# MySQL Master Slave Docker replication on two ec2 machines
This repository will help in creating master slave setup between two containers running on two different hosts

#### Prepare ANSIBLE Work Bench ####
1. Create an EC2 instance with public login enables
2. Set up the AWS Configuration
    1.  Run `aws configure` and provide "__AWS Access Key ID__" "__AWS Secret Access Key__"  
    2.  verify configuration by running `aws sts get-caller-identity`. Will provide the IAM user information
    3. Install boto3 using `pip install boto3`, Needed for ec2 management
3. Install Ansible using `pip install ansible`
4. Verify Ansible installation using `ansible --version`
5. Install GIT using `yum install git`
6. Clone repository using `git clone https://github.com/SatishNaidi/mysql-master-slave.git`
  

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
docker exec mysql_master sh -c "export MYSQL_PWD=f00bar; mysql -u root mydb -e 'SHOW MASTER STATUS\G'"
```
```
docker exec mysql_slave sh -c "export MYSQL_PWD=f00bar; mysql -u root mydb -e 'SHOW SLAVE STATUS\G'"
```

### Create a table in Master and Insert sample data ###

```
docker exec mysql_master sh -c "export MYSQL_PWD=f00bar; mysql -u root mydb -e 'create table table1(num int); insert into table values (12), (13)'"
```

### Check the replication on Slave ###

```
docker exec mysql_slave sh -c "export MYSQL_PWD=f00bar; mysql -u root mydb -e 'select * from table1 \G'"
```

#### Additional help in cleaning up dockers ####
https://zaiste.net/removing_docker_containers

### Command Line way of installing docker-compose ###
```
url=https://github.com/docker/compose/releases/download/1.16.1/docker-compose-`uname -s`-`uname -m`  
curl -L ${url} > ./docker-compose  
mv ./docker-compose /usr/bin/docker-compose  
chmod +x /usr/bin/docker-compose  
```

```
docker-compose -p slave -f slave-docker-compose.yml up -d  
docker-compose -p master -f master-docker-compose.yml up -d  
docker-compose -p master -f master-docker-compose.yml restart  
```
