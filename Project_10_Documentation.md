![](https://img.shields.io/badge/darey.io-orange)


# LOAD BALANCER SOLUTION WITH NGINX AND SSL/TLS

**In this project we will configure an Nginx Load Balancer solution.**

**It is also extremely important to ensure that connections to your Web solutions are secure and information is encrypted in transit – we will also cover connection over secured HTTP (HTTPS protocol), its purpose and what is required to implement it.**

**When data is moving between a client (browser) and a Web Server over the Internet – it passes through multiple network devices and, if the data is not encrypted, it can be relatively easy intercepted by someone who has access to the intermediate equipment. This kind of information security threat is called Man-In-The-Middle (MIMT) attack.**

**This threat is real – users that share sensitive information (bank details, social media access credentials, etc.) via non-secured channels, risk their data to be compromised and used by cybercriminals.**

**SSL and its newer version, TLS – is a security technology that protects connection from MITM attacks by creating an encrypted session between browser and Web server. Here we will refer this family of cryptographic protocols as SSL/TLS – even though SSL was replaced by TLS, the term is still being widely used.**

**SSL/TLS uses digital certificates to identify and validate a Website. A browser reads the certificate issued by a Certificate Authority (CA) to make sure that the website is registered in the CA so it can be trusted to establish a secured connection.**

## Task

*This project consists of two parts:*

1. Configure Nginx as a Load Balancer

2. Register a new domain name and configure secured connection using SSL/TLS certificates

**Your target architecture will look like this:**

![nginx image](https://user-images.githubusercontent.com/97651517/160321647-e7f5ec60-b2f6-4c5e-9b64-6ad48d234b82.png)

## LETS GET STARTED

### STEP 1. CONFIGURE NGINX AS A LOAD BALANCER

1. Create an EC2 VM based on Ubuntu Server 20.04 LTS and name it Nginx LB (do not forget to open TCP port 80 for HTTP connections, also open TCP port 443 – this port is used for secured HTTPS connections)

![Nginx instance created](https://user-images.githubusercontent.com/97651517/160321710-1fde4cd3-387f-46f4-8797-3388a553ace5.png)

![HTTP 80 and HTTPS 443 open ](https://user-images.githubusercontent.com/97651517/160321758-bac4e470-f4d5-4e06-826d-de5a7606bfa6.png)

2. Register a Free Domain Name (DNS) for the NGINX Server.

![register domain](https://user-images.githubusercontent.com/97651517/160321820-537447c4-c9ef-4439-ab2f-4a48aa6f9f30.png)

![Domain name registered](https://user-images.githubusercontent.com/97651517/160321862-38032d39-47a3-4284-aadb-fcba9c6c536c.png)

3. Created a hosted zone in Route53.

![create hosted zone](https://user-images.githubusercontent.com/97651517/160321936-c4d6c1eb-7cf3-43a1-8f17-01e6213df679.png)

4. Copy the following route traffic

![value route traffic](https://user-images.githubusercontent.com/97651517/160322026-de97d7a9-0d2a-4b01-bf45-e42e92211b98.png)

![name server](https://user-images.githubusercontent.com/97651517/160322097-5e7609f4-85a2-41f8-97eb-478b65594063.png)

5. Paste each in the domain name manage servers

![manage servers](https://user-images.githubusercontent.com/97651517/160322112-ff7656c9-8373-41ee-8744-af46177bb465.png)

![successfully done](https://user-images.githubusercontent.com/97651517/160324134-56801dd1-7fdd-4a5c-a30b-5956fa7bc69c.png)

6. Create a record for the Domain name

![create record](https://user-images.githubusercontent.com/97651517/160322243-56e119a2-2c65-4f6e-b8d0-ca45bc6d8eaa.png)

7. Input the Nginx Private IP addrress

![Nginx IP address](https://user-images.githubusercontent.com/97651517/160322321-4080f9ab-8811-42c8-9978-65678e1cacb9.png)

8. Create a second Record with www for the Domain

![second record www](https://user-images.githubusercontent.com/97651517/160322390-8377b9ee-81dd-46c3-9675-949759e02bc4.png)

9. Two Records Created Successfull

![two records created](https://user-images.githubusercontent.com/97651517/160322417-fd1a2136-b280-4a3f-80b0-d202a1dcc095.png)

### UPDATE SERVER AND INSTALL NGINX

`sudo apt update`

![sudo apt update nginx](https://user-images.githubusercontent.com/97651517/160322462-6f00f49c-e171-44ea-8725-3bef6a3f1dd0.png)

`sudo apt install nginx -y`

![install Nginx](https://user-images.githubusercontent.com/97651517/160322547-d419c48f-3cc1-47aa-83f2-c064b144e929.png)

1. Enabled nginx and started it using the following cmdlet;

2. Checked to see if nginx has been successfully installed using the following cmdlet;

`sudo systemctl enable nginx`

`sudo systemctl start nginx`

`sudo systemctl status nginx`

![sudo enable start and status nginx](https://user-images.githubusercontent.com/97651517/160322647-b70ba369-12cb-4bd3-8c48-0dd2394f1475.png)


3. Created a configuration for the reverse proxy settings by copying the web server configuration into the load balancer using the followng cmdlet;

*Created a config file in the NGINX sites-available folder as follows:*

`sudo nano /etc/ngingx/sites-available/load_balancer.conf`

![nano config file](https://user-images.githubusercontent.com/97651517/160322695-cfb07533-a5cc-4d91-8712-5aa04e47a740.png)

4. Removed the default site to allow the reverse proxy to redirect to the new configuration file using the cmdlet below:

`sudo rm -f /etc/nginx/sites-enabled/default`

5. Checked if nginx is successfully configured using the following cmdlet:

`sudo nginx -t`

6. Navigated to the nginx sites-enabled folder using the cmdlet below to link the config file created in the     sites-available with the sites-enabled:

`cd /etc/nginx/sites-enabled/`

7. Linked the Load Balancer config file created in the site-available to the site enabled so that the nginx can access the configuration to it using the cmdlet below:

`sudo ln -s ../sites-available/load_balancer.conf`

8. Reloaded nginx using the following cmdlet below:.

`sudo systemctl reload nginx`

*The following cmdlet above 4-8 shown below*

![nano config for nginx](https://user-images.githubusercontent.com/97651517/160322839-35ac6007-56c1-4134-8182-fcd9a22465cc.png)


9. Checked the 'http://toolingeon.ml' domain/site and saw that it redirects to the tooling page.
   But our site is unsecured so lets secure it with SSL/TLS CERTIFICATES in STEP 2.

![unsecured site from nginx server](https://user-images.githubusercontent.com/97651517/160322935-89acb98f-0954-4e17-8d71-5b25cbacb804.png)


### STEP 2. CONFIGURED SECURED CONNECTION USING SSL/TLS CERTIFICATES AS FOLLOWS:

1. Ran the following cmdlet below to install the certbot software.

`sudo apt install certbot -y`

![install certbot](https://user-images.githubusercontent.com/97651517/160323038-7da13a5e-b9e6-4483-b024-4587bb974c7b.png)

2. Installed a python dependency module to be used by the certbot using the following cmdlet: 

`sudo apt install python3-certbot-nginx -y`

![python 3 certbot](https://user-images.githubusercontent.com/97651517/160323082-41d7e7ff-c8fe-429a-b45b-a180e740c167.png)

3. Checked nginx syntax again and reloaded after the installation of certbot and python3 dependency.

`sudo nginx -t && sudo nginx -s`

![ngix -t and nginx -s](https://user-images.githubusercontent.com/97651517/160323138-78adf5c0-f398-4172-8ed4-9330db4f6cf6.png)

4. Created a certificate for our domain to secure it by running the following cmdlet:

`sudo certbot --nginx -d toolingeon.ml -d`

![certbot new certficate](https://user-images.githubusercontent.com/97651517/160323262-a74e75f8-f1ea-47a7-9c75-72bf6c0a3160.png)

5. Refreshed the 'toolingeon.ml' web page to observe the applied security settings after creating certificate

![site secured](https://user-images.githubusercontent.com/97651517/160323424-6de1f2a9-6bd8-4ac7-a82b-44b9ef535af2.png)


6. As best practice, created a crontab that will run a renew command periodically to automatically renew our certificate by editing the crontab

`crontab -e `

![crontab -e](https://user-images.githubusercontent.com/97651517/160323574-98d3146b-9c18-4ee4-9c0e-ec0b91937b1c.png)

script to the file, choose #1 for Nano editor: * */12 * * * root /usr/bin/certbot renew > /dev/null 2>&1

![crontab -e](https://user-images.githubusercontent.com/97651517/160323574-98d3146b-9c18-4ee4-9c0e-ec0b91937b1c.png)

## We have created an NGINX load balancer and can route traffic to it via reverse proxy and also secured it.