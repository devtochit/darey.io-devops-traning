![](https://img.shields.io/badge/darey.io-orange)

# Continuous Integration Pipeline For Tooling Website


## Table of Contents
- Introduction.
    - What is Jenkins?
    - Why Jenkins?
- Prerequisites.
- Installing and Configuring Jenkins Server.
    - Configure Jenkins to retrieve source code from GitHub using Webhooks.
- Configure Jenkins to copy files to the NFS server via ssh.

### Introduction
The previous projects allow us to add new web servers to our project and set up a load balancer to distribute the traffic between them. However, we still need to manually copy the files to the web servers. This project would allow us to automate the task of configuring multiple web servers.

In the world of software development, agility and speed of software solutions delivery are key factors. To achieve this, we need to automate as much as possible, as that would guarantee fast and repeatable deployments. In this project, we will start by automating part of our routine tasks with a free and open-source tool called Jenkins.
This process is very much wrapped around the concept of <b>Continous Integration (CI)</b>.
CI is a software development practice where developers integrate code into a shared repository frequently, preferably several times a day. Each integration can then be verified by an automated build and automated tests. CD is a software release process that uses automated testing to validate if changes to a codebase are correct and stable for immediate autonomous deployment to a production environment.

#### What is Jenkins?
Jenkins is a self-contained, open-source automation server that can be used to automate all sorts of tasks related to building, testing, and delivering or deploying software. Jenkins is written in Java and it is an open-source tool. Jenkins is a continuous integration tool. It is used to build and test your software projects continuously making it easier for developers to integrate changes to the project, and making it easier for users to obtain a fresh build. It also allows you to continuously deliver your software by integrating with a large number of testing and deployment technologies.

Jenkins can be installed through native system packages, Docker, or it can also run standalone on any machine with the java runtime environment installed.

#### Why Jenkins?
Jenkins is a continuous integration tool. It makes it easier for thousands of developers around the world to reliably build, test, and deploy their software. As it helps keep track of the version control system and initiate and monitor a build system if there are any changes.

In this project, we are going to utilize Jenkins CI capabilities to make sure that every change made to the source code in GitHub is automatically updated on the web servers.

### Prerequisites
- Source Code: <a href="https://github.com/manny-uncharted/tooling.git">GitHub</a>
- Infrastructure: AWS.
- Webserver Linux: Red Hat Enterprise Linux 8.
- Database Server: Ubuntu 20.04 + MySQL.
- Storage Server: Red Hat Enterprise Linux 8 + NFS Server.
- Load Balancer: Ubuntu 20.04.
- Programming Language: PHP.

To configure the above steps refer to the previous tutorial <a href="https://github.com/manny-uncharted/configuring-apache-as-a-load-balancer.git">Here</a>

<b>Task:</b> Enhance the architecture in the previous project by adding a Jenkins server, and configure a job to automatically deploy source code changes from Git to the NFS server.


### Installing and Configuring Jenkins Server
#### Install Jenkins on the Jenkins server.
- Create an AWS EC2 server based on Ubuntu Server 20.04 LTS and name it "Jenkins".




- Install JDK (Java Development Kit) on the Jenkins server. Since Jenkins is a Java-based application.
```
sudo apt update
sudo apt install default-jdk-headless
```



- Install Jenkins
```
wget -q -O - https://pkg.jenkins.io/debian-stable/jenkins.io.key | sudo apt-key add -
sudo sh -c 'echo deb https://pkg.jenkins.io/debian-stable binary/ > \
    /etc/apt/sources.list.d/jenkins.list'
sudo apt update
sudo apt-get install jenkins
```



- We have to make sure that Jenkins is running.
```
sudo systemctl status jenkins
```


- By default Jenkins server uses TCP port 8080 - open it by creating a new inbound rule in your EC2 security group.



- Perform initial Jenkins setup. From your browser access.
```
http://<Jenkins-Server-Public-IP-Address>:8080
```



<b>Note</b>:You will be prompted to provide a default admin password.

- To get retrieve the default admin password, run the following command.
```
sudo cat /var/lib/jenkins/secrets/initialAdminPassword
```


- Then you will be asked which plugins to install - choose suggested plugins.



- Once plugin installation is done - create an admin user and you will get your Jenkin server address.



#### Configure Jenkins to retrieve source code from GitHub using Webhooks.
Here we will configure Jenkins to automatically retrieve source code from GitHub whenever there is a change in the source code. This would be done by using Webhooks.

- Enable webhooks in your GitHub repository settings. Go to your GitHub repository and click on Settings > Webhooks > Add webhook.



- Go to Jenkins web console, click "New Item" and create a Freestyle project.



- To connect your GitHub repository to Jenkins, you will need to provide its URL, just copy the URL from your GitHub repository.



- In your Jenkins project, the configurations, choose Git repository, provide the URL of your GitHub repository and click on "Add" to add the credentials, so Jenkins can access your GitHub repository.



- Save the configurations and let's try to run the build. Click on "Build Now" and you will see the build is successful.



You can open the build and check in "Console Output" if it has run successfully.

If so – congratulations! You have just made your very first Jenkins build!

Note: This build does not produce anything and it runs only when we trigger it manually. Let us fix it.

- In your Jenkins project, click on "Configure" your job/project and add these two configurations
    - Build Triggers > Build when a change is pushed to GitHub, triggering the job from the GitHub webhook:




    - Configure "Post-build Actions" to archive all the files – files resulting from a build are called "artifacts".



- Now, let's go ahead and make some changes in any file in your GitHub repository (e.g. README.MD file) and push the changes to the master branch.



- We should see that a new build has been launched automatically (by webhook) and you can see its results – artifacts, saved on the Jenkins server.


Note: You have now configured an automated Jenkins job that receives files from GitHub by webhook trigger (this method is considered as ‘push’ because the changes are being ‘pushed’ and file transfer is initiated by GitHub). There are also other methods: trigger one job (downstream) from another (upstream), poll GitHub periodically and others.

- By default, the artifacts are stored on Jenkins server locally
```
ls /var/lib/jenkins/jobs/tooling_github/builds/<build_number>/archive/
```





#### Configure Jenkins to copy files to the NFS server via ssh.
Now we have our artifacts stored locally on the Jenkins server, the next step is to copy them to the NFS server to the /mnt/apps directory.

As Jenkins is highly extendable and can be configured to do almost anything, we will need a plugin that is called "Publish over SSH" to copy files to the NFS server.

- Install "Publish over SSH" plugin. Go to Jenkins web console, click on "Manage Jenkins" > "Manage Plugins" > "Available" > "Publish over SSH" > "Install without restart".



- Configure the job/project to copy artifacts over to the NFS server. On the main dashboard select "Manage Jenkins" and choose the "Configure System" menu item. Scroll down to Publish over SSH plugin configuration section and configure it to be able to connect to your NFS server. The configuration is pretty straightforward, you just need to provide the following details:
    - Provide a private key (contents of .pem file that you use to connect to NFS server via SSH/Putty)
    Arbitrary name
    Hostname – can be private IP address of your NFS server
    Username – ec2-user (since NFS server is based on EC2 with RHEL 8)
    Remote directory – /mnt/apps since our Web Servers use it as a mounting point to retrieve files from the NFS server

And then save the configurations.



Note: Test the configuration and make sure the connection returns Success. Remember, that TCP port 22 on the NFS server must be open to receive SSH connections.

- Now open the Jenkins project configuration page and add another one "Post-build Actions" to copy the artifacts to the NFS server. You are selecting the option "Send build artifacts over SSH".

- Now configure it to send all files produced by the build into our previously defined remote directory /mnt/apps. . In our case we want to copy all files and directories – so we use ** and then save the configurations.

If you want to apply some particular pattern to define which files to send – <a href="http://ant.apache.org/manual/dirtasks.html#patterns">use this syntax</a>. 




- Now, let's go ahead and make some changes in any file in your GitHub repository (e.g. README.MD file) and push the changes to the master branch. Webhook will trigger the build and the artifacts will be copied to the NFS server.



- To make sure that the files in /mnt/apps have been udated – connect via SSH/Putty to your NFS server and check README.MD file
```
cat /mnt/apps/README.md
```



Now we have just implemented our first Continous Integration solution using Jenkins CI.