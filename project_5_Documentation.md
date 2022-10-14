# CLIENT/SERVER ARCHITECTURE USING A MYSQL RELATIONAL DATABASE MANAGEMENT SYSTEM

## Client-server architecture with mysql


Create and configure two Linux-based virtual servers (EC2 instances in AWS).
Server A name - `mysql server`
Server B name - `mysql client`

On mysql server Linux Server install MySQL Server software

![server](./project_5_images/server.png)

On mysql client Linux Server install MySQL Client software

![client](./project_5_images/client.png)

`sudo vi /etc/mysql/mysql.conf.d/mysqld.cnf`

![bind](./project_5_images/project1.png)

From mysql client Linux Server, connect remotely to mysql server Database Engine without using SSH.

## Server configuration 

![localhost](./project_5_images/Local-host.PNG.jpg)

New database

![new-database](./project_5_images/Created-new-database.PNG)



Connection from client side

![Access](./project_5_images/Accessed-new-user.PNG)

Show databases


![database](./project_5_images/Created-new-database.PNG)