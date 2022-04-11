# Mysql Master-Slave-Slave Replication using AWS EC2 Instance.

The master-slave replication process allows database administrators to duplicate or copy data stored on more than one server simultaneously. This allows the database administrator to create a live backup of the database all the time.

---
## Preparations:

I suggest you to use the privite IP for the server communication ( We are using AWS ec2 instance and the privite IP wll communcate here properly ). Also I suggest you to enable the port 3306 on your server security firewall.

We need 3 servers for configuring the Master-Slave-Slave. The servers in this example have the following IPs:

Master  IP: 172.31.12.78
Slave-1 IP:  172.31.12.23
Slave–2 IP: 172.31.19.123

-----
## Configure the Master Server
Open the MySQL configuration file and add the following lines in the [mysqld] section:

~~~
vi /etc/my.cnf

bind-address           = 0.0.0.0
server-id              = 1
log_bin                = mysql-bin
~~~

Then restart the MySQL service for changes to take effect

~~~
systemctl restart mysqld
~~~

The next step is to create a new replication user. Login to MYSQL server as root and create user. Please use the followng  SQL queries that will create the user and grant the REPLICATION SLAVE privilege to the user:

~~~
mysql> CREATE USER 'relication_user'@'%' IDENTIFIED BY 'strong_password';

mysql> GRANT REPLICATION SLAVE ON *.* TO 'relication_user'@'%';

mysql> FLUSHPRIVILEGES;
~~~

Verify the status of the master 
~~~
mysql> SHOW MASTER STATUS\G

Output
*************************** 1. row ***************************
             File: mysql-bin.000001
         Position: 679
     Binlog_Do_DB: 
 Binlog_Ignore_DB: 
Executed_Gtid_Set: 
1 row in set (0.00 sec)
Take note of file name, ‘mysql-bin.000001’ and Position ‘679’. You’ll need these values when configuring the slave server. 
~~~

Take note of file name, ‘mysql-bin.000001’ and Position ‘679’. You’ll need these values when configuring the slave server. These values will probably be different on your server.

-------
## Configure the Slave-1 and Slave-2 Server

Here we have 2 Slvae servers and we want to connect 2 Slave servers to Master server. The slave-1 and slave-2 server MySQL configuration file open and edit the following lines:

>>Slave-1
~~~
vi  /etc/my.cnf

bind-address           = 0.0.0.0
server-id              = 2
log_bin                = mysql-bin
~~~

>>Slave-2
~~~
vi  /etc/my.cnf

bind-address           = 0.0.0.0
server-id              = 3
log_bin                = mysql-bin
~~~

Then Restart MySQL service in the slave-1 and slave-2 ( both ) server:

~~~
systemctl restart mysqld
~~~

The next step is to configure the parameters that the slave server will use to connect to the master server. First Login to the 2 slave server MySQL shell and run the commands in the both server.

~~~
mysql -u root -p
~~~
First, stop the slave threads and run the following query that will set up the slave to replicate the master:

~~~
mysql> STOP SLAVE;

mysql> CHANGE MASTER TO MASTER_HOST='72.31.12.78', MASTER_USER='relication_user', MASTER_PASSWORD='strong_password', MASTER_LOG_FILE='mysql-bin.000001', MASTER_LOG_POS=679;
~~~
Please confirm you are using the correct IP address, user name, and password. The log file name and position must be the same as the values you obtained from the master server.

Once done, start the slave threads.
~~~
mysql> START SLAVE;

mysql> SHOW SLAVE STATUS\G;

output
MariaDB [(none)]> SHOW SLAVE STATUS\G
*************************** 1. row ***************************
               Slave_IO_State: Waiting for master to send event
                  Master_Host: 72.31.12.78
                  Master_User: relication_user
                  Master_Port: 3306
                Connect_Retry: 60
~~~

----
## Test the Configuration

To verify that everything works as expected, I have created a new database on the master server and checked both slave servers, and confirmed the database was automatically created in the Slave servers. You can follow the command to check the database is created or not in the slave server.

~~~
Mysql -u root -p 

mysql>  SHOW DATABASES;
~~~

You can see that database in the Slave-1 and Slave-2 server. Mysql Master Slave replication completed.

---
## Conclusion
In this tutorial, I have shown you set up a Wordpress Installation with Docker Compose file. Now the WordPress up and running. This is an easier way to set up the WordPress using docker.



### ⚙️ Connect with Me

 <p align="center">
<a href="mailto:aparthan275@gmail.com"><img src="https://img.shields.io/badge/Gmail-D14836?style=for-the-badge&logo=gmail&logoColor=white"/></a>
<a href="https://www.instagram.com/_r.e.b.e.l.z_33/"><img src="https://img.shields.io/badge/Instagram-E4405F?style=for-the-badge&logo=instagram&logoColor=white"/></a>
<a href="https://www.linkedin.com/in/abhiraj-parthan-82038b191"><img src="https://img.shields.io/badge/LinkedIn-0077B5?style=for-the-badge&logo=linkedin&logoColor=white"/></a> 
<a href="https://www.wppredirect.tk/go/?p=918893532145&m=Abhiraj%20Parthan."><img src="https://img.shields.io/badge/WhatsApp-25D366?style=for-the-badge&logo=whatsapp&logoColor=white"/></a>
  </a></p>
</div>


