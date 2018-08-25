# MySQL Master Slave Docker replication on two ec2 machines
This repository will help in creating master slave setup between two containers running on two different hosts

#### Prepare Ansible Work Bench ####
1. Create an EC2 instance with public login enables
2. Set up the AWS Configuration
    1.  Run `aws configure` and provide "__AWS Access Key ID__" "__AWS Secret Access Key__"  
    2.  verify configuration by running `aws sts get-caller-identity`. Will provide the IAM user information
    3. Install boto3 using `pip install boto3`, Needed for ec2 management
3. Install Ansible using `pip install ansible`
4. Verify Ansible installation using `ansible --version`
5. Install GIT using `yum install git`
6. Clone repository using `git clone https://github.com/SatishNaidi/mysql-master-slave.git`
  
#### Run Ansible Playbook ####
- Edit [ansible.cfg](https://github.com/SatishNaidi/mysql-master-slave/blob/master/ansible) with appropriate location of PEM to gain the log in access
    - private_key_file=<AbsolutePath>/<FileName>.pem  
    - Example: private_key_file=/Users/naisa13/mykeys/Mumbai.pem
- `cd mysql-master-slave`  
- Run play book as `ansible-playbook host_creation.yml`

### Additional Information about the Repo ###  
        
 Playbook `host_creation.yml` make use of AWS Default configuration, refer to step 2. Will create 2 t2.micro instances, with embedded configuration in `host_creation.yml` tags each of them as Master and Slave Playbook connects to each of the instance and installs docker, git, mysql client, docker-compose

 In each of the instance, container will be created with `master-docker-compose.yml` and `slave-docker-compose.yml`
 
 ### Connection to MySQL ###
- `master_hostname=<Master's hostname>`
- `slave_hostname=<Slave's hostname>`
- `db_name=mydb`
- `mysql -h ${master_hostname} -P 3002 -uroot -pf00bar` #to connect to Master
- `mysql -h ${slave_hostname} -P 3003 -uroot -pf00bar` #to connect to Slave

### To Verify Replication Status ###
- `mysql -h ${master_hostname} -P 3002 -uroot -pf00bar -e 'SHOW MASTER STATUS\G'`
- `mysql -h ${slave_hostname} -P 3003 -uroot -pf00bar -e 'SHOW SLAVE STATUS\G'`

### Create a table in Master and Insert sample data ###
```
mysql -h ${master_hostname} -P 3002 -uroot -pf00bar ${db_name} -e 'create table table1(num int);'
mysql -h ${master_hostname} -P 3002 -uroot -pf00bar ${db_name} -e 'insert into table values (12), (13)'
```
### Check the replication on Slave ###
```
mysql -h ${slave_hostname} -P 3003 -uroot -pf00bar ${db_name} -e 'select * from table1 \G'
```
