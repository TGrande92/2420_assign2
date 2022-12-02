# 2420_assign2

### Step 1 - Setting up DigitalOcean droplets, firewall, loadbalancer and VPC

We are going to start by creating infastructure on digital ocean. This process will entail setting up a VPC, a loadbalancer and a firewall that are all connected to two droplets.

![image](/images/vpc.png)

As you can see in this image, there is a vpc created in the area SFO3 representing (San Fransisco 3). Connected to this VPC we have 2 droplets Ubuntu 1 and Ubuntu 2, as well as our loadbalancer.

You will be able to see in the next image that we have a firewall that will cover our loadbalancer.

![image](/images/firewall.png)

You can attach all of these compenents together by using a tag that associates each of these parts within digitalocean.

### Step 2 - Creating new users and disabling root on droplets

Lets start with creating new users on the droplets and disabling root. This will create a user, give it sudo priveledges and move your ssh key

```
useradd -ms /bin/bash username
usermod -aG sudo username
rsync --archive --chown=username:username ~/.ssh /home/username
```

It's good practice to disable root ssh
```
sudo vim /etc/ssh/sshd_config
```

[image](/images/rootlog.png)

set this to no if its yes and we can continue on to the next step!

### Step 3 - Install a webserver on each droplet

On this step we are going to begin to prepare each droplet by installing caddy

[image](/images/getcaddy.png)

Here we recieve a file named caddy_2.6.2_linux_amd64.tar.gz and we will extract it

[image](/images/tarcaddy.png)

and using the two commands 

```
sudo chown root: caddy
sudo cp caddy /usr/bin/
```

This will move our caddy file to the bin directory and allow root to own caddy.

### Step 4 - Preparing our web app

We will start by making an html and src directory, and then create index.html

[images](/images/indexhtml.png)

We will fill index.html with the following

[images](/images/html.png)

For the last part of this step we have a few things to do to initiate our web app

```
curl https://get.volta.sh | bash
source ~/.bashrc
volta install node
```

This will install node on your droplet or wsl and we can then create a new node project in the src directory

```
npm init
npm i fastify
```

[image](/images/npminst.png)

[image](/images/npmfast.png)

and then we can edit our index.js

[image](/images/vimindexjs.png)

### Step 5 - Lets create our Caddyfile and the caddy.service

Still using our wsl because we don't want to write these files twice and we can put them using sftp later.

Lets start with the Caddyfile

[image](/images/cadfill.png)

and the caddy.service

[image](/images/caddyserv.png)

### Step 6

During this step we are going to repeat step 4 but this time on our two droplets

ssh into your droplets using powershell

```
ssh -i /path/to/ssh/key username@droplet_ip
```

[images](images/volta2.png)

install volta and node

### Step 7 - Ensure it runs

We are going to write a service file using WSL that will start and restart our node application if the service fails.

[images](/images/s7service.png)

### Step 8 - The fun part!

Lets use sftp on wsl to connect to both droplets move all of these new files we have created and place them in their respective places, start them and test them.


[images](/images/sftp1.png)

[images](/images/sftp2.png)

[images](/images/sftp3.png)

Great! We have moved all the neccessary files to one droplet, lets repeat this again for the other droplet and

[images](/images/sftp4.png)

Okay! Let's test our .service files on both of our droplets
```
systemctl start caddy.service
systemctl enable caddy.service
systemctl status caddy.service

systemctl start network.service
systemctl enable network.service
systemctl status network.service
```
