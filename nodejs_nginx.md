# Setup Node.js with Ngnix in production on Ubuntu

## Install Node.js

1. To install Node.js
```
sudo apt-get update
sudo apt-get install nodejs
```

2. Then, install the Node package manager
```
sudo apt-get install npm
```

3. Create a symbolic link for "node"
```
sudo ln -s /usr/bin/nodejs /usr/bin/node
```

4. Now test both node and npm commands working
```
node - v
v0.10.25
npm -v
1.3.10
```

## Install Ngnix

* Install Ngnix
```
sudo apt-get install nginx
```

## Hello World Code

* Edit sample Hello World web application
```
vi hello.js
```

* Insert the following code into the file, and be sure to substitute the app server's private IP address for both of highlighted APP_PRIVATE_IP_ADDRESS items. If you want to, you may also replace the highlighted port, 8080, in both locations (be sure to use a non-admin port, i.e. 1024 or greater):
``` hello.js
var http = require('http');
http.createServer(function (req, res) {
  res.writeHead(200, {'Content-Type': 'text/plain'});
  res.end('Hello World\n');
}).listen(8080, 'APP_PRIVATE_IP_ADDRESS');
console.log('Server running at http://APP_PRIVATE_IP_ADDRESS:8080/');
```

## Setup Ngnix as reverse proxy to serve http request

* Open default server block configuration file for editing
```
sudo vi /etc/nginx/sites-available/default
```

* Delete everything in the file and insert the following configuration. Be sure to substitute your own domain name for the server_name directive (or IP address if you don't have a domain set up), and the app server private IP address for the APP_PRIVATE_IP_ADDRESS. Additionally, change the port (8080) if your application is set to listen on a different port:
```
server {
    listen 80;

    server_name example.com;

    location / {
        proxy_pass http://APP_PRIVATE_IP_ADDRESS:8080;
        proxy_http_version 1.1;
        proxy_set_header Upgrade $http_upgrade;
        proxy_set_header Connection 'upgrade';
        proxy_set_header Host $host;
        proxy_cache_bypass $http_upgrade;
    }
}
```

* Restart Nginx
```
sudo service nginx restart
```

## Setup Ngnix as reverse proxy to serve https request