# ANSIBLE REFACTORING AND STATIC ASSIGNMENTS (IMPORTS AND ROLES)


## In this project we will continue working with ansible-config-mgt repository and make some improvements of our code. Now we need to refactor our Ansible code, create assignments, and learn how to use the imports functionality. Imports allow to effectively re-use previously created playbooks in a new playbook – it allows you to organize your tasks and reuse them when needed.


## Code Refactoring 

Refactoring is a general term in computer programming. It means making changes to the source code without changing expected behaviour of the software. The main idea of refactoring is to enhance code readability, increase maintainability and extensibility, reduce complexity, add proper comments without affecting the logic.

*In our case, we will move things around a little bit in the code, but the overal state of the infrastructure remains the same* 

## Let us see how you can improve your Ansible code!


### Step 1 – Jenkins job enhancement

Before we begin, let us make some changes to our Jenkins job – now every new change in the codes creates a separate directory which is not very convenient when we want to run some commands from one place. Besides, it consumes space on Jenkins serves with each subsequent change. Let us enhance it by introducing a new Jenkins project/job – we will require Copy Artifact plugin.


NB: Jenkins build previously below


`ls /var/lib/jenkins/jobs/ansible/builds`

![jenkins build in terminal](https://user-images.githubusercontent.com/97651517/168493108-1e3915bd-97cd-4047-a0c9-f8623f58bcf5.png)




1. Go to your Jenkins-Ansible server and create a new directory called ansible-config-artifact – we will store there all artifacts after each build.

`sudo mkdir /home/ubuntu/ansible-config-artifact`

![new dir ansible-config-artifact](https://user-images.githubusercontent.com/97651517/168493196-8fe8a9ed-688f-419c-a2db-9f0e35d24582.png)


2. Change permissions to this directory, so Jenkins could save files there – chmod -R 0777 /home/ubuntu/ansible-config-artifact

`sudo chmod -R 0777 /home/ubuntu/ansible-config-artifact`


![change permission](https://user-images.githubusercontent.com/97651517/168493300-9e9e4fc7-1482-4d1d-bdff-c4b7632da1f4.png)


3. Go to Jenkins web console -> Manage Jenkins -> Manage Plugins -> on Available tab search for Copy Artifact and install this plugin without restarting Jenkins.


![manage jenkins plugins](https://user-images.githubusercontent.com/97651517/168496979-f4201890-7dd7-4ba4-b722-1196ae1ef950.png)


![copy artifacts](https://user-images.githubusercontent.com/97651517/168497034-73a47287-030b-42c6-9678-b9a4ccd7d86f.png)



4. Create a new Freestyle project (you have done it in Project 9) and name it save_artifacts.

![freestyle project](https://user-images.githubusercontent.com/97651517/168497173-e3111c14-cf07-4c35-9f41-ad5754f49ef9.png)



5. This project will be triggered by completion of your existing ansible project. Configure it accordingly:


![general 1](https://user-images.githubusercontent.com/97651517/168497212-7a553e20-13e4-4d80-b6e0-265eeac13ac6.png)




![source code 2](https://user-images.githubusercontent.com/97651517/168497224-1dffcf85-8883-4ce6-a4bb-6167aac63c28.png)




### Note: You can configure number of builds to keep in order to save space on the server, for example, you might want to keep only last 2 or 5 build results. You can also make this change to your ansible job.


6. The main idea of save_artifacts project is to save artifacts into /home/ubuntu/ansible-config-artifact directory. To achieve this, create a Build step and choose Copy artifacts from other project, specify ansible as a source project and /home/ubuntu/ansible-config-artifact as a target directory.


![build artifact](https://user-images.githubusercontent.com/97651517/168497287-ac69402b-22bc-4d56-b988-0ec517d2033d.png)


![copy artifacts success](https://user-images.githubusercontent.com/97651517/168497281-1024216f-2f67-4317-bef7-68eb8e05e5f8.png)



7. Test your set up by making some change in README.MD file inside your ansible-config-mgt repository (right inside master branch).
If both Jenkins jobs have completed one after another – you shall see your files inside /home/ubuntu/ansible-config-artifact directory and it will be updated with every commit to your master branch.

Now your Jenkins pipeline is more neat and clean.


![test artifact](https://user-images.githubusercontent.com/97651517/168497347-db54b1a6-0da6-4359-b504-9b6bf9e2ec7d.png)


![readme in repo](https://user-images.githubusercontent.com/97651517/168497360-d0be7174-c86a-4e12-b1bb-877343496e03.png)


![build in jenkins](https://user-images.githubusercontent.com/97651517/168497374-78c0e04b-9197-4e2d-8b23-cef365e993a3.png)


![build was successful](https://user-images.githubusercontent.com/97651517/168497384-b99a8202-2e86-4554-8bbb-3ddb8586dc8d.png)


![files copied to jenkins](https://user-images.githubusercontent.com/97651517/168497395-abae53a2-04cd-4d0f-bfc5-41bec2de4c05.png)



## REFACTOR ANSIBLE CODE BY IMPORTING OTHER PLAYBOOKS INTO SITE.YML


### Step 2 – Refactor Ansible code by importing other playbooks into site.yml


*Before starting to refactor the codes, ensure that you have pulled down the latest code from master (main) branch, and created a new branch, name it refactor.*

DevOps philosophy implies constant iterative improvement for better efficiency – refactoring is one of the techniques that can be used, but you always have an answer to question "why?". Why do we need to change something if it works well?

In the previous project we wrote all tasks in a single playbook common.yml, now it is pretty simple set of instructions for only 2 types of OS, but imagine we have many more tasks and we need to apply this playbook to other servers with different requirements. In this case, we will have to read through the whole playbook to check if all tasks written there are applicable and is there anything that we need to add for certain server/OS families. Very fast it will become a tedious exercise and our playbook will become messy with many commented parts. Our DevOps colleagues will not appreciate such organization of your codes and it will be difficult for them to use our playbook.


Most Ansible users learn the one-file approach first. However, breaking tasks up into different files is an excellent way to organize complex sets of tasks and reuse them.


Let see code re-use in action by importing other playbooks.



1. Within playbooks folder, create a new file and name it site.yml – This file will now be considered as an entry point into the entire infrastructure configuration. Other playbooks will be included here as a reference. In other words, site.yml will become a parent to all other playbooks that will be developed. Including common.yml that you created previously.


2. Create a new folder in root of the repository and name it static-assignments. The static-assignments folder is where all other children playbooks will be stored. This is merely for easy organization of your work. It is not an Ansible specific concept, therefore you can choose how you want to organize your work. You will see why the folder name has a prefix of static very soon.


3. Move common.yml file into the newly created static-assignments folder


4. Inside site.yml file, import common.yml playbook.



![site yml](https://user-images.githubusercontent.com/97651517/168497455-cc99fb1a-4b57-4bf7-8002-6c2acec52255.png)



NB: The code above uses built in import_playbook Ansible module.



*Your folder structure should look like this;


![folder structure](https://user-images.githubusercontent.com/97651517/168497514-eb68d430-23da-4968-9eee-3b25de47cbca.png)


5. Run ansible-playbook command against the dev environment


6. Since you need to apply some tasks to your dev servers and wireshark is already installed – you can go ahead and create another playbook under static-assignments and name it common-del.yml. In this playbook, configure deletion of wireshark utility.


![commm del yml playbook to delete wireshack](https://user-images.githubusercontent.com/97651517/168497548-a0e51c7a-ffb2-4f81-81c1-f53aa63c3407.png)


**Update site.yml with - import_playbook: ../static-assignments/common-del.yml instead of common.yml and run it against dev servers**


![update common yml to del yml](https://user-images.githubusercontent.com/97651517/168497584-a279f44d-541b-4e68-b46f-de4c62ede873.png)


**Ping your servers to see wether it will respond*


`ansible all -m ping`


*if it doesnt send a pong respopnd from target nodes (servers) then you have to congiure your Ansible config file to make sure it is pointing to your inventory directory.

- cd into inventory from your ansible-config-artifact.

- The use pwd to get the present working directory.

- Copy it, that is the inventory directory you will use in the ansible config file.

- Then run this command against the inventory directory to open the ansible config file.


`sudo vi /etc/ansible/ansible.cfg`


![vi ansible config](https://user-images.githubusercontent.com/97651517/168497613-210265e7-5255-4cb0-9bd3-5d784e7d892e.png)


![copy path to inventory and replace then comment](https://user-images.githubusercontent.com/97651517/168497624-98cd2bed-7e75-491f-a14b-6e8a83f68929.png)



Run the Ping module command again



`ansible all -m ping`


![new ping pong](https://user-images.githubusercontent.com/97651517/168497675-072af863-3eae-4b4e-b40a-32fcac38ca88.png)



- Now that we can ping to our servers or target nodes lets run our playbook to remove wireshark for each nodes, so lets cd back into our ansible-config-artifact directory.


`cd ..`


`ansible-playbook -i /home/ubuntu/ansible-config-artifact/inventory/dev.yml /home/ubuntu/ansible-config-artifact/playbooks/site.yml`

or

`ansible-playbook -i inventory/dev.yml playbooks/site.yml`


![succesfully deleted wireshack](https://user-images.githubusercontent.com/97651517/168497703-8e9a5823-921d-4f12-8da4-bc09b4bfd3cf.png)



### Make sure that wireshark is deleted on all the servers by running wireshark --version


`wireshark --version`


![wireshark removed](https://user-images.githubusercontent.com/97651517/168497734-bfc7526a-857b-472b-a883-ee92fdfac7b7.png)



**Now you have learned how to use import_playbooks module and you have a ready solution to install/delete packages on multiple servers with just one command.*



# CONFIGURE UAT WEBSERVERS WITH A ROLE 'WEBSERVER'


- First lets create an new branch called 'refactor' from our main branch in our Github.


`git status`


`git checkout -b refactor`


![create a branch called refactor](https://user-images.githubusercontent.com/97651517/168497766-3d6169f4-9f70-45b9-88f5-19e920ea1c3b.png)




### Step 3 – Configure UAT Webservers with a role ‘Webserver’


We have our nice and clean dev environment, so let us put it aside and configure 2 new Web Servers as uat. We could write tasks to configure Web Servers in the same playbook, but it would be too messy, instead, we will use a dedicated role to make our configuration reusable.


1. Launch 2 fresh EC2 instances using RHEL 8 image, we will use them as our uat servers, so give them names accordingly – Web1-UAT and Web2-UAT


![2 new web servers created](https://user-images.githubusercontent.com/97651517/168497843-bd8aa76a-6f8d-4657-b11a-64c14737bbf0.png)



2. To create a role, you must create a directory called roles/, relative to the playbook file or in /etc/ansible/ directory.


There are two ways how you can create this folder structure:


a. Use an Ansible utility called ansible-galaxy inside ansible-config-mgt/roles directory (you need to create roles directory upfront)

mkdir roles
cd roles
ansible-galaxy init webserver

b. Create the directory/files structure manually
Note: You can choose either way, but since you store all your codes in GitHub, it is recommended to create folders and files there rather than locally on Jenkins-Ansible server.

The entire folder structure should look like below, but if you create it manually.

After removing unnecessary directories and files, the roles structure should look like this:


![folder structure](https://user-images.githubusercontent.com/97651517/168497891-36fd55d0-9678-4642-bb2f-626cdd4aa8e2.png)



![folders and files created](https://user-images.githubusercontent.com/97651517/168497927-92c59a95-7d9b-45d7-9bc6-9a41b973605e.png)



NOTE: Ensure you are using ssh-agent to ssh into the Jenkins-Ansible instance.



For Windows users – [title](https://www.youtube.com/watch?v=OplGrY74qog)

For Linux users – [title](https://www.youtube.com/watch?v=OplGrY74qog)



3. Update your inventory ansible-config-mgt/inventory/uat.yml file with IP addresses of your 2 UAT Web servers



![uat yml file](https://user-images.githubusercontent.com/97651517/168498165-99b4adee-812b-4151-8d45-fc951705e828.png)



4. In /etc/ansible/ansible.cfg file uncomment roles_path string and provide a full path to your roles directory roles_path    = /home/ubuntu/ansible-config-mgt/roles, so Ansible could know where to find configured roles.


`sudo vi /etc/ansible/ansible.cfg`



![sudo etc](https://user-images.githubusercontent.com/97651517/168498224-d2daf055-98c0-4992-9a70-00edebf836b9.png)



5. It is time to start adding some logic to the webserver role. Go into tasks directory, and within the main.yml file, start writing configuration tasks to do the following:


- Install and configure Apache (httpd service)

- Clone Tooling website from GitHub https://github.com/<your-name>/tooling.git.

- Ensure the tooling website code is deployed to /var/www/html on each of 2 UAT Web servers.

- Make sure httpd service is started


Your main.yml may consist of following tasks:

  

![new task](https://user-images.githubusercontent.com/97651517/168498279-e77307b4-9062-4a2c-ae29-4bbc71fab92f.png)



# REFERENCE WEBSERVER ROLE


### Step 4 – Reference ‘Webserver’ role

- Within the static-assignments folder, create a new assignment for uat-webservers uat-webservers.yml. This is where you will reference the role.

  
  
![uati-webservers](https://user-images.githubusercontent.com/97651517/168498327-6288a008-939a-4e78-9288-1685a4997dfa.png)



Remember that the entry point to our ansible configuration is the site.yml file. Therefore, you need to refer your uat-webservers.yml role inside site.yml.

So, we should have this in site.yml
  
  
![site yml updated](https://user-images.githubusercontent.com/97651517/168498371-e766a253-af18-4db5-a716-bc675aa101ce.png)

  

### Step 5 – Commit & Test


Commit your changes, create a Pull Request and merge them to master branch, make sure webhook triggered two consequent Jenkins jobs, they ran successfully and copied all the files to your Jenkins-Ansible server into /home/ubuntu/ansible-config-mgt/ directory.
  
  

![compare pull request](https://user-images.githubusercontent.com/97651517/168498492-d699975c-1d68-40e5-9931-95414f71fee6.png)

  

![create pull request](https://user-images.githubusercontent.com/97651517/168498502-53e69d31-408b-42d4-8fe7-bf0eb6086cbc.png)

  

![merge pull request](https://user-images.githubusercontent.com/97651517/168498513-be9ad23f-bb6c-44d5-a46d-9ec1b9b74959.png)

  

![comfirm merge](https://user-images.githubusercontent.com/97651517/168498527-3e8d8472-0974-4c6e-a2c7-184bcd03039c.png)

  

![merge succesfull](https://user-images.githubusercontent.com/97651517/168498550-2f91c02c-6b89-4059-89e3-56ef08ae7674.png)




**Now run the playbook against your uat inventory and see what happens:*

- NB: Before running your playbook, to avoid any connection error use the ping module command to ping your UAT SERVERS first before running your playbook against UAT.



`ansible uat-webservers -m ping`

  

![ping uat-webservers sucessfull](https://user-images.githubusercontent.com/97651517/168498631-d2dfe25e-e43e-44b0-b624-c46f2be6a1e4.png)

  
  
`sudo ansible-playbook -i /home/ubuntu/ansible-config-mgt/inventory/uat.yml /home/ubuntu/ansible-config-mgt/playbooks/site.yaml`



  
![second playbook sucessful](https://user-images.githubusercontent.com/97651517/168498668-8535ebff-4b66-4c43-831b-f0d9b2b9b3d6.png)



### You should be able to see both of your UAT Web servers configured and you can try to reach them from your browser:


http://<Web1-UAT-Server-Public-IP-or-Public-DNS-Name>/index.php

or

http://<Web1-UAT-Server-Public-IP-or-Public-DNS-Name>/index.php


Your Ansible architecture now looks like this:

  
  
![final architecture](https://user-images.githubusercontent.com/97651517/168498724-cd1b22fe-1650-4bf5-9b6f-6acba6b33343.png)

  
  
  
  
  
  
