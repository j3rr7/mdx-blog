### Deploying Nginx to Server

This is a guide on how to deploy and configure Nginx on a server.
This guide assumes you already have a server running.
This guide uses Ubuntu 22.04 as the server OS.

## Installing Nginx

To install Nginx on Ubuntu, run the following command:

```bash
sudo apt install nginx
```

If there's a prompt to confirm the installation, press `y` and then press `Enter`.
This will install Nginx on your server.

## Configuring Nginx

To configure Nginx, run the following command:

```bash
cd /etc/nginx/sites-available
```

This will take you to the directory where Nginx configuration files are stored.
There is one file in this directory called `default`.
`default` is the default configuration file for Nginx.

We can use this file as a template to create our own configuration file.

To create a new configuration file, run the following command:

```bash
touch new-config.conf
```

This will create a new file called `new-config.conf` in the same directory.

To modify the file, run the following command:

```bash
nano new-config.conf
```

This will open the file in the `nano` text editor. You can use any text editor you want. ex: `vim`, `emacs`, `gedit`,
etc.

our default configuration file looks like this:

```nginx
server {
    listen 80;
    listen [::]:80;
    
    root /var/www/html;
    index index.html index.htm index.nginx-debian.html;
    
    location / {
        try_files $uri $uri/ =404;
    }
}
```
---
### Let's break down the configuration file:

```nginx
server {
    listen 80;
    listen [::]:80;
```

This tells Nginx to listen on port 80. This is the default port for HTTP traffic.
by default, Nginx will listen on IPv4 port 80. and with the second line, it will also listen on IPv6 port 80.
Usefull if you have an IPv6 address.


```nginx
    root /var/www/html;
    index index.html index.htm index.nginx-debian.html;
```
This tells Nginx to serve files from the `/var/www/html` directory.
`index.html` is the default file that Nginx will serve if a user visits the root of the domain.

```nginx
    location / {
        try_files $uri $uri/ =404;
    }
}
```
This line tells Nginx to serve files from the root directory if the user visits the root of the domain.
If the user visits a directory that doesn't exist, Nginx will return a 404 error.

## Testing the configuration file
To test the configuration file, run the following command:
```bash
sudo nginx -t
```
If the configuration file is valid, you will see the following output:
```bash
nginx: the configuration file /etc/nginx/nginx.conf syntax is ok
```
If the configuration file is invalid, you will see errors in the output.

## Enabling the configuration file
We have created a new configuration file, but Nginx is not using it yet.
To enable the configuration file, run the following command:
```bash
sudo ln -s /etc/nginx/sites-available/new-config.conf /etc/nginx/sites-enabled/
```
This will create a symbolic link from the configuration file to the `sites-enabled` directory.
Nginx will use the configuration file in the `sites-enabled` directory.

## Restarting Nginx
To restart Nginx, run the following command:
```bash
sudo systemctl restart nginx
```
or alternatively, you can run:
```bash
sudo nginx -s reload
```

## Conclusion
You have successfully deployed Nginx on your server.

---

### Setting up Nginx as reverse proxy
Assuming you have a web application running on port 3000 , you can use Nginx as a reverse proxy to serve the web application.

First check if the web application is running by visiting the IP address of the server on port 3000.
```bash
curl http://<server-ip>:3000
```
change `server-ip` into the IP address of your server.

If you see the web application, you can continue to the next step.

If you don't see the web application, you need to check the logs of the web application to see what's wrong.

We will create a new configuration file for the web application.
```nginx
server {
    listen 80;
    listen [::]:80;
    
    location / {
        proxy_pass http://localhost:3000;
    }
}
```

Breakdown:
```nginx
    location / {
        proxy_pass http://localhost:3000;
    }
```
`proxy_pass` directive is used in Nginx configuration to specify the URL of the proxied server to which the request should be forwarded.
and this tells Nginx to forward all requests to the web application running on port 3000.

Let's add additional configuration to the configuration file to make it more secure.
```nginx
server {
    listen 80;
    listen [::]:80;
    
    location / {
        proxy_pass http://localhost:3000;
        proxy_set_header Host $host;
        proxy_set_header X-Real-IP $remote_addr;
        proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
    }
}
```
Let's break down the additional configuration:
```nginx
        proxy_set_header Host $host;
        proxy_set_header X-Real-IP $remote_addr;
        proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
```
These lines tell Nginx to set the `Host` header to the value of the `Host` request header.

By setting the Host header to the value of $host, Nginx is ensuring that the proxied server receives the same host name as the original client's request, allowing the proxied server to correctly determine which website or application should handle the request.

This can be important in cases where the proxied server is hosting multiple websites or applications, as it ensures that requests are properly directed to the correct resource.

Setting `proxy_set_header X-Real-IP $remote_addr;` in Nginx configures the `X-Real-IP` header in the request sent to the proxied server to be the IP address of the original client making the request.
and set the `X-Real-IP` header to the value of the `remote_addr` variable.

By setting the `X-Real-IP` header to the value of $remote_addr, Nginx is ensuring that the proxied server receives the original client's IP address, allowing the proxied server to make decisions based on the client's IP address such as rate limiting, access control, or logging.

`proxy_set_header X-Real-IP $remote_addr;` allows you to pass the original client's IP address to the proxied server, providing additional information that may be useful for security, monitoring, or other purposes.

The `X-Forwarded-For` header is used to track the path of a request as it passes through multiple proxies. It is a comma-separated list of IP addresses, where the left-most IP address represents the original client, and each subsequent IP address represents an intermediate proxy that the request passed through.

`proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;` provides the proxied server with information about the path of the request and the original client's IP address, allowing it to make decisions based on this information, such as access control, rate limiting, or logging.

Ok now we have a new configuration file for the web application, let's enable it.
```bash
sudo ln -s /etc/nginx/sites-available/new-config.conf /etc/nginx/sites-enabled/
```
and restart Nginx.
```bash
sudo systemctl restart nginx
```

Now you can visit the IP address or domain pointing to your ip address of the server to see the web application.
example `http://<server-ip>` or `http://<domain>`

## Next Section 
[Setting up Nginx with Let's Encrypt to enable HTTPS]() 
