## NGINX: 
- Nginx is typically known as web server. It has another definition 
- When Nginx is the one that Gets the requests and forwards them to the server and then serves back the response: this is knwon as 'reverse proxy 
- NGINX can act as a load balancer 
## Configure NGINX:
- When you install nginx what it will is it creates a directory under 'usr/local/etc/nginx. and if you open it up you will see a bunch of different files
- `nginx.conf` : Is the configuration file for configuring nginx the reverse proxy
- Key value pairs are known as `directives`
- Block of codes inside curly brackets are known as `context` 
- The `mime.types` file contains all the types to be served(Header: `content-type`: text/css) and to add it to your server you need to add the `include mime.types`
- inside the Server context we have:
 ```conf
 server { 
    listen 8080 ; 
    client max body_size 30;
    # root Directive that indicates the file path that contains a bunch of different files that we want to serve when we go to 8080 
  }
```
```conf
upstream backendserver {
    server 127.0.0.1:1111; 
    server 127.0.0.1:3333; 
    server 127.0.0.1:4444; 
    server 127.0.0.1:7777;
}
location / {
    proxy_pass http://backendserver/ *This configuration will allow load balancing around the different servers
}

location /car { root /Users/Desktop/mysite; #This will append /fruits to the root alias /Users/DesktopJmysite/car #If you use this it will not append /car to the end #When you use this what we serve is the index.html file under the folder that mentionned and if the index file is not found we will get a 403 Forbidden error
try files /car/cars.html /index.html =404 #what this will do is it will try to find the files /car/cars.html but if it does not find it will serve index.html and if non off these exist it will throf a 404 error 
} 
location ~* /count/[0-9] {#We use this to add regular expressions 
root /Users/Desktop/mysite;
try files /index.html =404; 
} 

location /crops { 
return 307 /fruits; # this will redirect to /fruits 
rewrite ^/number/(\w+) /count/$1;
}
```