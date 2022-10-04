![](https://img.shields.io/badge/darey.io-orange)

# PROJECT 4: MEAN STACK IMPLEMENTATION

MEAN STACK DEPLOYMENT TO UBUNTU IN AWS

Step 0:  Preparing prerequisites

- Sign in to AWS free tier account and create a new EC2 Instance of t2.nano family with Ubuntu Server 20.04 LTS or 22.04 LTS, (HVM) image.
  
- Connect to this instance via SSH.

Task:

In this assignment you are going to implement a simple Book Register web form using MEAN stack.

Step 1: Install NodeJs

Update ubuntu

`sudo apt update`


Upgrade ubuntu

`sudo apt upgrade`

![Ubuntu upgraded](project_4_images/1.PNG)

Add certificates:

`sudo apt -y install curl dirmngr apt-transport-https lsb-release ca-certificates`

`curl -sL https://deb.nodesource.com/setup_18.x | sudo -E bash -`

Install NodeJS:

`sudo apt install -y nodejs`



Step 2: Install MongoDB

`sudo apt-key adv --keyserver hkp://keyserver.ubuntu.com:80 --recv 0C49F3730359A14518585931BC711F9BA15703C6`

`echo "deb [ arch=amd64 ] https://repo.mongodb.org/apt/ubuntu trusty/mongodb-org/3.4 multiverse" | sudo tee /etc/apt/sources.list.d/mongodb-org-3.4.list`

![keyserver](project_4_images/1.PNG)

`sudo apt install -y mongodb`

Start The server:

`sudo service mongodb start`

Verify that the service is up and running:

`sudo systemctl status mongodb`

![Mongo service](project_4_images/2.PNG)

Install npm – Node package manager:

`sudo apt install -y npm`



Install body-parser package:

`sudo npm install body-parser`



Create a folder named ‘Books’

`mkdir Books && cd Books`

In the Books directory, Initialize npm project:

`npm init`



Add a file to it named server.js

`vi server.js`

![Open file](project_4_images/3.PNG)

Edit the file


Step 3: Install Express and set up routes to the server

Install express and mongoose in Books folder

`sudo npm install express mongoose`



Create a folder named apps in the Books folder

`mkdir apps && cd apps`


Create a "routes.js" file in apps folder

`vi routes.js`

![edit routes.js](project_4_images/Edit-routesjs.PNG)

Create a folder named models in the "apps" folder

`mkdir models && cd models`



Create a file named book.js

`vi book.js`



Input the script

![Edit file](project_4_images/edit-booksjs.PNG)

Step 4 – Access the routes with AngularJS

Change the directory back to ‘Books’

`cd ../..`

Create a folder 'public' in Books

`mkdir public && cd public`



Add a file named script.js

`vi script.js`





Create a file named index.html in public folder

`vi index.html`



![Edit file](project_4_images/edit-index-html.PNG)

Change the directory back up to Books

`cd ..`

Start the server

`node server.js`

![server running](project_4_images/node.PNG)

The server is now up and running

Connect it via port 3300
## Authors

👤 **Uzoma Jesse Tochukuw**

- GitHub: [@Hedevedup](https://github.com/githubhandle)
- Twitter: [@devtochi](https://twitter.com/twitterhandle)
- LinkedIn: [jesseuzoma](https://linkedin.com/in/linkedinhandle)




## Show your support

Give a ⭐️ if you like this project!

## Acknowledgments
- Hat tip to anyone whose code was used
- Inspiration
- etc  
**`many thanks to Darey.io for this dev ops traning`**

