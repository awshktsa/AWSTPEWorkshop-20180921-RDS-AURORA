# AWSTPEWorkshop-20180921-RDS-AURORA
For this workshop, we introduce the main feature with Aurora, also combinded with RDS-MySQL. It helps audience to understand the difference between Aurora and My-SQL. We also talk about the Read-Replica setting, how we convert or migrate our database from RDS-MySQL to Aurora

------

### Step 1:
* Switch Region on the AWS console, a drag down menu near right-up corner.
Pick one region close to you.

------

### Step 2: Create a Aurora DB (Serverless)
* **AWS Console > Services > RDS > Create Database > Select Enging > Amazon Aurora**

![AWS Workshop Series - createrdsaurora](https://github.com/juntinyeh/AWSTPEWorkshop-20180921-RDS-AURORA/blob/master/images/createrdsaurora.png)

- We go through latest type "Serverless" for our workshop first

![AWS Workshop Series - auroraserverless](https://github.com/juntinyeh/AWSTPEWorkshop-20180921-RDS-AURORA/blob/master/images/auroraserverless.png)

- We go through latest type "Serverless" for our workshop first, and setup the *identifer*, *DB user*, and *DB Password*.

![AWS Workshop Series - inputdbuserpassword](https://github.com/juntinyeh/AWSTPEWorkshop-20180921-RDS-AURORA/blob/master/images/inputdbuserpassword.png)

- Next step, we need to setup the *Resource Allocation* for your Aurora Serverless.

![AWS Workshop Series - auroraserverlesscapacity](https://github.com/juntinyeh/AWSTPEWorkshop-20180921-RDS-AURORA/blob/master/images/auroraserverlesscapacity.png)

- Select the *Parameter Group* for your database instance.

![AWS Workshop Series - pickupparametergroup](https://github.com/juntinyeh/AWSTPEWorkshop-20180921-RDS-AURORA/blob/master/images/pickupparametergroup.png)

- Select DB *VPC* and *Subnet Group* for network setting.

![AWS Workshop Series - dbsubnetgroup](https://github.com/juntinyeh/AWSTPEWorkshop-20180921-RDS-AURORA/blob/master/images/dbsubnetgroup.png)

------

### Step 3:
* Check if you already have a EC2 Key pair in your selected region. 
* If not, create one through **AWS Console > EC2 > Key Pairs > Create Key Pair**. 
* Remember to download the private key(.pem) and well saved. 
* In usual, we will move it into ~/.ssh/ sub-folder in your home directory.
* To make it secure, remeber to change the privilege with command 
``` chmod 0400 XXXXX.pem ```

* If you are windows user, and you should download putty.exe and puttygen.exe from [Here](https://www.chiark.greenend.org.uk/~sgtatham/putty/latest.html). And also check the document [Here](https://www.ssh.com/ssh/putty/windows/puttygen) if you don't know how to convert .pem to .ppk

------

### Step 4:
* Launch a new EC2 instance to operate your database in VPC
- **AWS Console > Services > EC2 > Launch Instance **
- Select Amazon Linux 2 with *Free tier eligible* label, with proper instance type (ex: t2.micro / t3.micro)
- Locate this instance in the same VPC, with public IP attached and SSH security group granted.
- set the userdata with following content

```
#!/bin/sh
yum install mysql
```

- Connect to your EC2, and run the following command to test your database is alive.
```
> mysql -h YOUR-AURORA-ENDPOINT -u YOUR-AURORA-USER -p
```

------

### Step 5:
* Practice how to take a DB Snapshot and restore to a new instance/cluster
- **AWS Console > Services > RDS > Cluster**
- Select Correct DB which you created in Step2 > Actions > Take snapshot
- Give a name to this snap shot


![AWS Workshop Series - dbbackuprestore](https://github.com/juntinyeh/AWSTPEWorkshop-20180921-RDS-AURORA/blob/master/images/backuprestore.png)

------

### Step 6:
Restore a snapshot to a new Instance
- **AWS Console > Services > RDS > Snapshots**
- Select Correct Snapshot you created in Step5 > Actions > Restore Snapshot
- Pick *Provisioned* this time, and enable the **Back Track**

![AWS Workshop Series - aurorabacktrack](https://github.com/juntinyeh/AWSTPEWorkshop-20180921-RDS-AURORA/blob/master/images/aurorabacktrack.png)

------

### Step 7:
* Create a new RDS-MySQL Cluster with Multi-AZ setting
- **AWS Console > Services > RDS > Instances**
- Create Instance > Select DB Engine as MySQL
- Pick **Dev/Test - MySQL**, and go next step
- Click **Multi-AZ** and set the identifer, account and password.
- Check the Failover feature, and reboot feature

------

### Step 8:
* Check ReadReplica on your RDS instance

![AWS Workshop Series - replicapromote](https://github.com/juntinyeh/AWSTPEWorkshop-20180921-RDS-AURORA/blob/master/images/replicapromote.png)

- Start from the MySQL Instance you created at Step7 > Instance Actions
- You can create a MySQL ReadReplica 
- You can also create an Aurora ReadReplica, we choose this one

![AWS Workshop Series - aurorareplica](https://github.com/juntinyeh/AWSTPEWorkshop-20180921-RDS-AURORA/blob/master/images/aurorareplica.png)

- Once your reaplica is ready, please check the Instance list, and you will find "Promote Read Replica" Feature now is available. This feature can help you to clone a latest database for branch, testing and verification purpose.

![AWS Workshop Series - promotereplica](https://github.com/juntinyeh/AWSTPEWorkshop-20180921-RDS-AURORA/blob/master/images/promotereplica.png)

------

```
Note:
We have setup our first Aurora Instance as Serverless mode, 
some of you might already found that, you cannot find your db in "Instance List". 
We have set the timer to 5 minutes as default, if your serverless db has no access in last 5 minutes, 
it will going into sleep mode right away.

Try to connect your database through your EC2 again, and it will back to live right away.
```

------

`Workshop Challenge:`

Try to edit the Parameter Group for more advanced setting
- **AWS Console > Services > RDS > Parameter Group > Create Parameter Group** 

![AWS Workshop Series - parametergroup](https://github.com/juntinyeh/AWSTPEWorkshop-20180921-RDS-AURORA/blob/master/images/parametergroup.png)

- Give a proper name, and select correct group family. 
- Click your parameter group > Edit Parameter
- Setup the *binlog_format* anything but `OFF`
- Create a mysql-slave on your EC2, set the master as your RDS server.
* You might want to check the official document (https://dev.mysql.com/doc/refman/8.0/en/replication-howto.html) from MySQL

## After Workshop
1. Remember to delete the EC2, all the RDS and Aurora Instance, and check those snapshot we created in workshop.
