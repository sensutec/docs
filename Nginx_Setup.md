# Setting up Nginx (in an Ubuntu VM)

**Important Directories**
```
# Global Nginx Config
/etc/nginx/nginx.conf

# Nginx Server Blocks
/etc/nginx/sites-available
/etc/nginx/sites-enabled

# Path to Repo
/var/www/example-com
```

## Install Nginx

Make sure Nginx is installed
```
sudo apt-get update
sudo apt-get install nginx
```

## Add Directory to Serve Files

Set up directory to serve files
```
cd /var/www/example-com
```

## Configure the Nginx Block

First, copy the default configuration.
```
cd /etc/nginx/sites-available
cp default example-com
```

Remove the **symbolic link** to the default server block so that it doesn't interfere with our custom server block.
```
sudo rm /etc/nginx/sites-enabled/default
```

Configure the block with `sudo nano example.com`
```
server {
    listen 80 default_server;
    listen [::]:80 default_server ipv6only=on;

    # Set Serve Directory
    root /var/www/example-com;
    
    # Set Entry Point
    index index.html index.htm;

    server_name example.com;

    location / {
        try_files $uri $uri/ =404;
    }
}
```

**Important**
You'll an to set your `server_name example.com` if you have domain you'd like to use, if you want to use a "dummy" domain, you can set it equal `localhost`. We'll configure our `hosts` file in a later step.

## Enable Server Block (copy to sites-enabled)

**Important**
You must use the *full, absolute path* for the symbolic link. The link will not work properly if you use a relative path.

Create a symbolic link into `sites-enabled` folder. 
```
sudo ln -s /etc/nginx/sites-available/example.com /etc/nginx/sites-enabled/
```

Note: The command `ln` creates a link and the `-s` makes the link symbolic. A symbolic link points to another file by filename and works even if the file doesn't exist. A "hard" link, which is the default behavior, points to the data stored on the disk. Hard linking is prevented on many operating system in fear of disrupting the file system.

## Enable Hash Buckets for Process Caching

Uncomment Nginx line specifying size of cache line.
```
sudo nano /etc/nginx/nginx.conf
``` 

Uncomment line specifying `server_names_hash_bucket_size 64`
```
# Remove comment from this line
server_names_hash_bucket_size 64;
```

Note: The "hash bucket size" corresponds to size of the cache line allotted for key searches in a hash table. Valid values are 32, 64, 128. Read more about Nginx hash bucket size [here](http://nginx.org/en/docs/hash.html).

Restart Nginx to load the new configuration
```
sudo service nginx restart
```

---

## Configure Local Computer to Intercept DNS Requests (Optional)
*Useful for accessing domains not configured through DNS (e.g., local.example.com)*

Log out of the virtual server
```
exit
# enter until on local machine
```

On your local machine, open your hosts file
```
sudo nano /etc/hosts
```

Add your public IP and dummy domain
```
# 
10.x.x.x    local.example.com
```

Navigating to `local.example.com` should now serve up your `index.html` file.

---

## Debugging

Check if you can connect to your server from *within* your VM
```
wget http://localhost/
```

Ping your server from *outside* of your VM
```
ping my.public.ip
```

### Debugging with Amazon Web Services

In EC2 Instances, you must change your security group settings. Select the security group that corresponds to your instance.

#### Allow `ping my.public.ip` from anywhere
Click the "Inbound" tab and add a rule.
```
Type: All ICMP, Protocol: All, Port Range: N/A (or default), Source: 0.0.0.0/0
Type: HTTP, Protocol: TCP, Port: 80, Source: 0.0.0.0/0
```

### Make sure Nginx is running
```
curl -v localhost
sudo iptables -t nat -nvL
```

### Resolve Nginx Errors
```
nginx -t
```




