### ***INSTALL AND CONFIGURE ANSIBLE ON EC2 INSTANCE*** ###

Update the name tag on EC2 Instance to Jenkins-Ansible. We will use this server to run playbooks.

![](images/1.PNG)

In your GitHub account create a new repository and name it ansible-config-mgt.
![](images/2.PNG)

Instal Ansible
![](images/3.PNG)

Check Ansible version by running 
```
ansible --version
```
![](images/4.PNG)

Configure Jenkins build job to save your repository content every time you change it

Create a new Freestyle project ansible in Jenkins and point it to your ‘ansible-config-mgt’ repository.
![](images/5.PNG)

To connect to my GitHub repository, I copied the ansible-config-mgt GitHub repository HTTPS url code and paste it in Jenkins Ansible source code management, created a credentials so that Jenkins could access files in the repository then saved the configuration

![](images/6.PNG)

![](images/7.PNG)

Click "Configure" on Ansible project and add these two configurations

- Configure triggering the job from GitHub webhook "GitHub hook trigger for GITScm polling"
![](images/8.PNG)

Configure Webhook in GitHub and set webhook to trigger ansible build.
![](images/10.PNG)


Configure "Post-build Actions" to archive all the files – files resulted from a build are called "artifacts".
![](images/9.PNG)

Test setup by making some change in README.MD file in master branch and commit changes.
![](images/11.PNG)

Make sure that builds starts automatically and Jenkins saves the files (build artifacts) in following folder
```
ls /var/lib/jenkins/jobs/Ansible/builds/7/archive/
```
![](images/12.PNG)

Clone down your ansible-config-mgt repo to your Jenkins-Ansible instance
```
git clone https://github.com/taiwoakintunde/ansible-config-mgt.git
```
![](images/13.PNG)

### ***BEGIN ANSIBLE DEVELOPMENT*** ###

Create a new branch that will be used for development of a new feature in ansible-config-mgt GitHub repository

Checkout the newly created feature branch to your local machine and start building your code and directory structure
![](images/14.PNG)

Create a directory and name it playbooks – it will be used to store all your playbook files.
![](images/15.PNG)

Create a directory and name it inventory – it will be used to keep your hosts organised.
![](images/16.PNG)

Within the playbooks folder, create your first playbook, and name it common.yml
![](images/17.PNG)

Within the inventory folder, create an inventory file (.yml) for each environment (Development, Staging Testing and Production) dev, staging, uat, and prod respectively.
![](images/18.PNG)

***Set up an Ansible Inventory***

>Note: Ansible uses TCP port 22 by default, which means it needs to ssh into target servers from Jenkins-Ansible host – for this you can implement the concept of ssh-agent. Now you need to import your key into ssh-agent:
![](images/20.PNG)


ssh into your Jenkins-Ansible server using ssh-agent
![](images/21.PNG)

Update your inventory/dev.yml file with this snippet of code:

```
[nfs]
<NFS-Server-Private-IP-Address> ansible_ssh_user='ec2-user'

[webservers]
<Web-Server1-Private-IP-Address> ansible_ssh_user='ec2-user'
<Web-Server2-Private-IP-Address> ansible_ssh_user='ec2-user'

[db]
<Database-Private-IP-Address> ansible_ssh_user='ec2-user' 

[lb]
<Load-Balancer-Private-IP-Address> ansible_ssh_user='ubuntu'
```
![](images/22.PNG)

### ***CREATE A COMMON PLAYBOOK*** ###

It is time to start giving Ansible the instructions on what needs to be performed on all servers listed in inventory/dev.

>In common.yml playbook, let's write the configuration for repeatable, re-usable, and multi-machine tasks that is common to systems within the infrastructure.

Update your playbooks/common.yml file with the following code:

```
---
- name: update web, nfs and db servers
  hosts: webservers, nfs, db
  remote_user: ec2-user
  become: yes
  become_user: root
  tasks:
    - name: ensure wireshark is at the latest version
      yum:
        name: wireshark
        state: latest

- name: update LB server
  hosts: lb
  remote_user: ubuntu
  become: yes
  become_user: root
  tasks:
    - name: Update apt repo
      apt: 
        update_cache: yes

    - name: ensure wireshark is at the latest version
      apt:
        name: wireshark
        state: latest
```
![](images/23.PNG)

> This playbook is divided into two parts, each of them is intended to perform the same task: install wireshark utility (or make sure it is updated to the latest version) on your RHEL 8 and Ubuntu servers. It uses root user to perform this task and respective package manager: yum for RHEL 8 and apt for Ubuntu.

Commit your code into GitHub:

Use git commands to add, commit and push your branch to GitHub.

```
git status

git add <selected files>

git commit -m "commit message"
```
![](images/24.PNG)

Create a Pull request (PR)
![](images/25.PNG)

Click on compare & pull request, if everything looks Ok, click create a pull request
![](images/26.PNG)

Click Merge pull request and confirm merge
![](images/27.PNG)

Confirmation Jenkins automatic build
![](images/28.PNG)

Check the artifacts on the terminal using this command:
```
ls /var/lib/jenkins/jobs/Ansible/builds/8/archive/
```
![](images/29.PNG)

Switched back to branch main

```
git checkout main 

# Pull down the latest changes.
git pull
```
![](images/30.PNG)

### ***RUN FIRST ANSIBLE TEST*** ###

Now, it is time to execute ansible-playbook command and verify if your playbook actually works:

Run first Ansible Playbook - change to the build artifacts directory
```
cd /var/lib/jenkins/jobs/Ansible/builds/8/archive
```
![](images/31.PNG)


Run ansible playbook
```
ansible-playbook -i inventory/dev.yml playbooks/common.yml
```

We need to check if wireshark has been installed on each server by running `which wireshark` or `wireshark --version`
![](images/33.PNG)
![](images/34.PNG)

Update the ansible playbook with some new Ansible tasks: 
`checkout -> change codes -> commit -> PR -> merge -> build -> ansible-playbook`

Create a new branch
`git branch new-ansibleTasks` and switch to the new branch `git checkout new-ansibleTasks`

![Alt text](images/35.PNG)

Update the playbook by: 
- Create a new directory and a file inside it.
- Change timezone on all servers

```
- name: Create a new directory named New-Dir
  file:
   path: ~/New-Dir
   state: directory

- name: Create a file named new-file
  file:
   path: ~/New-Dir/new-file
   state: touch

- name: set timezone to Asia/Tokyo
  timezone:
     name: Asia/Tokyo
```
![Alt text](images/36.PNG)

The changes will be pushed to github by running:
```
git add --all
git commit -m "new ansible tasks"
git push new-ansibleTasks
```
![Alt text](images/37.PNG)

Pull request and merge changes
![Alt text](images/38.PNG)

Check the new build in Jenkins
![Alt text](images/39.PNG)

Run the Ansible Playbook - change to the build artifacts directory
```
cd /var/lib/jenkins/jobs/Ansible/builds/19/archive
ansible-playbook -i inventory/dev.yml playbooks/common.yml
```
![Alt text](images/40.PNG)


![Alt text](images/41.PNG)

![Alt text](images/42.PNG)














