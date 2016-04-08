# Serving Git Repo Live from Ubuntu Server (with Nginx)

## From Ubuntu Server

Install necessary dependencies
```
sudo apt-get update
sudo apt-get install nginx
sudo apt-get install git
```

Create bare git directory (for hooking)
```
cd ~
mkdir example.git && cd example.git
git init --bare
```

Create "post-receive" hook
```
cd hooks
touch post-receive
sudo nano post-receive
```

Enter the following code to run *after* push has completed
```
#!/bin/sh
# --work-tree is the location of the repo
# --git-dir is the location of the .git file we just created
sudo git --work-tree=/var/www/example --git-dir=/home/ubuntu/example.git checkout -f
sudo service nginx restart
```

**Important**
Make sure you have execute privelages on the `post-receive` file you just created (`chmod 700 post-receive`).

## From Local Machine

Copy public key to your instace
```
cat ~/.ssh/id_rsa.pub | ssh -i ~/.ssh/your_pemfile.pem ubuntu@your.ip.address "cat>> .ssh/authorized_keys"
```

Add "live" remote to your repo. Make sure you add the path to your example.git that we created in the Ubuntu server.
```
cd example
git remote add live ssh://username@your.ip.address/home/ubuntu/example.git
```

You should now be able to push to the `live` remote, effectively updating the files in your Ubuntu server.


```
#!/bin/bash

clear

ssh -i "/Users/himmel/scripts/stec.pem" himmel@ec2-52-53-237-87.us-west-1.compute.amazonaws.com
```