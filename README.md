# Docker Assignment 1 - Part 3

## Step 1: Create the volume:
PS C:\Users\syedshah\Documents\GitHub\assignment1-part3> docker volume create my_volume
my_volume
PS C:\Users\syedshah\Documents\GitHub\assignment1-part3> docker volume ls
DRIVER    VOLUME NAME
local     my_volume
PS C:\Users\syedshah\Documents\GitHub\assignment1-part3> docker volume inspect my_volume
[
    {
        "CreatedAt": "2026-10-07T10:22:17Z",
        "Driver": "local",
        "Labels": null,
        "Mountpoint": "/var/lib/docker/volumes/my_volume/_data",
        "Name": "my_volume",
        "Options": null,
        "Scope": "local"
    }
]

## Step 2: nginx container with the volume:
PS C:\Users\syedshah\Documents\GitHub\assignment1-part3> docker run -d --name nginx-web -p 8080:80 -v my_volume:/usr/share/nginx/html nginx
Unable to find image 'nginx:latest' locally
latest: Pulling from library/nginx
f7e1db57b58b: Pull complete 
1efc53192927: Pull complete 
04bdbc7ea6b5: Pull complete 
807ab930f3a0: Pull complete 
947d22046304: Pull complete 
8020170dc65f: Pull complete 
a1b85ab28c90: Download complete 
c329293b1d90: Download complete 
Digest: sha256:f9ea18bfa4fad859e1ed38259d711da7ccad2c3516e875cec3351a57c859f571
Status: Downloaded newer image for nginx:latest
c4d7dc711d84c591818cf251c8f6a1f13edf007a85302ee834af7f9c569d963f
PS C:\Users\syedshah\Documents\GitHub\assignment1-part3> docker ps
CONTAINER ID   IMAGE                          COMMAND                  CREATED          STATUS          PORTS                                         NAMES
c4d7dc711d84   nginx                          "/docker-entrypoint.…"   4 seconds ago    Up 3 seconds    0.0.0.0:8080->80/tcp, [::]:8080->80/tcp       nginx-web
6994005580c4   syedshah3994/hello-flask:1.0   "python app.py"          27 minutes ago   Up 12 minutes   0.0.0.0:5002->5000/tcp, [::]:5002->5000/tcp   webapp-renamed
094a00193310   syedshah3994/hello-flask:1.0   "python app.py"          49 minutes ago   Up 49 minutes   0.0.0.0:5001->5000/tcp, [::]:5001->5000/tcp   test-app

## Step 3: Verify the nginx default page:
PS C:\Users\hamza\Documents\GitHub\assignment1-part3> curl.exe http://localhost:8080
<!DOCTYPE html>
<html>
<head>
<title>Welcome to nginx!</title>
<style>
html { color-scheme: light dark; }
body { width: 35em; margin: 0 auto;
font-family: Tahoma, Verdana, Arial, sans-serif; }
</style>
</head>
<body>
<h1>Welcome to nginx!</h1>
<p>If you see this page, nginx is successfully installed and working.
Further configuration is required for the web server, reverse proxy, 
API gateway, load balancer, content cache, or other features.</p>

<p>For online documentation and support please refer to
<a href="https://nginx.org/">nginx.org</a>.<br/>
To engage with the community please visit
<a href="https://community.nginx.org/">community.nginx.org</a>.<br/>
For enterprise grade support, professional services, additional 
security features and capabilities please refer to
<a href="https://f5.com/nginx">f5.com/nginx</a>.</p>

<p><em>Thank you for using nginx.</em></p>
</body>
</html>

## Copy it to the volume:
PS C:\Users\syedshah\Documents\GitHub\assignment1-part3> docker cp index.html nginx-web:/usr/share/nginx/html/index.html
Successfully copied 70B (transferred 2.05kB) to nginx-web:/usr/share/nginx/html/index.html

## Verify at localhost:8080:
PS C:\Users\syedshah\Documents\GitHub\assignment1-part3> curl.exe http://localhost:8080
<html><body><h1>Hello from my_volume - index.html</h1></body></html>

## Stop and remove the container:
PS C:\Users\syedshah\Documents\GitHub\assignment1-part3> docker stop nginx-web
nginx-web
PS C:\Users\syedshah\Documents\GitHub\assignment1-part3> docker rm nginx-web
nginx-web
PS C:\Users\syedshah\Documents\GitHub\assignment1-part3> docker ps -a
CONTAINER ID   IMAGE                          COMMAND           CREATED             STATUS                         PORTS                                         NAMES
6994005580c4   syedshah3994/hello-flask:1.0   "python app.py"   34 minutes ago      Up 19 minutes                  0.0.0.0:5002->5000/tcp, [::]:5002->5000/tcp   webapp-renamed
094a00193310   syedshah3994/hello-flask:1.0   "python app.py"   56 minutes ago      Up 56 minutes                  0.0.0.0:5001->5000/tcp, [::]:5001->5000/tcp   test-app
81804e597c02   syedshah3994/hello-flask:1.0   "python app.py"   57 minutes ago      Created                                                                      my-app
ad176c8f65bb   hello-world                    "/hello"          About an hour ago   Exited (0) About an hour ago                                                 cool_elion

## httpd container with the same volume:
PS C:\Users\syedshah\Documents\GitHub\assignment1-part3> docker run -d --name httpd-web -p 8081:80 -v my_volume:/usr/local/apache2/htdocs httpd
Unable to find image 'httpd:latest' locally
latest: Pulling from library/httpd
d19953f56d19: Pull complete 
31730ab78c03: Pull complete 
62aa6b76421e: Pull complete 
0bdb50ccb97e: Pull complete 
4f4fb700ef54: Pull complete 
2e5e9538f241: Download complete 
ae5940b419b0: Download complete 
Digest: sha256:c842ac797bd1fe8b3cd26d002ac9d948aa2814097544600600b35aef42e6785a
Status: Downloaded newer image for httpd:latest
fa075d9092a6edc7a4a3e6f45f7bbbbae61c7d0196e0a872b2f13b8a044032e9
PS C:\Users\syedshah\Documents\GitHub\assignment1-part3> docker ps
CONTAINER ID   IMAGE                          COMMAND              CREATED          STATUS          PORTS                                         NAMES
fa075d9092a6   httpd                          "httpd-foreground"   33 seconds ago   Up 33 seconds   0.0.0.0:8081->80/tcp, [::]:8081->80/tcp       httpd-web
6994005580c4   syedshah3994/hello-flask:1.0   "python app.py"      35 minutes ago   Up 20 minutes   0.0.0.0:5002->5000/tcp, [::]:5002->5000/tcp   webapp-renamed
094a00193310   syedshah3994/hello-flask:1.0   "python app.py"      58 minutes ago   Up 58 minutes   0.0.0.0:5001->5000/tcp, [::]:5001->5000/tcp   test-app

## Verify the httpd page at localhost:8081:
PS C:\Users\syedshah\Documents\GitHub\assignment1-part3> curl.exe http://localhost:8081
<html><body><h1>Hello from my_volume - index.html</h1></body></html>

## Copy it to the volume:
PS C:\Users\syedshah\Documents\GitHub\assignment1-part3> docker cp about.html httpd-web:/usr/local/apache2/htdocs/about.html
Successfully copied 69B (transferred 2.05kB) to httpd-web:/usr/local/apache2/htdocs/about.html

## Verify at localhost:8081/about.html:
PS C:\Users\syedshah\Documents\GitHub\assignment1-part3> curl.exe http://localhost:8081/about.html
<html><body><h1>About page - served from my_volume</h1></body></html>

##  Stop and remove the container:
PS C:\Users\syedshah\Documents\GitHub\assignment1-part3> docker stop httpd-web
httpd-web
PS C:\Users\syedshah\Documents\GitHub\assignment1-part3> docker rm httpd-web
httpd-web
PS C:\Users\syedshah\Documents\GitHub\assignment1-part3> docker ps -a
CONTAINER ID   IMAGE                          COMMAND           CREATED             STATUS                   PORTS                                         NAMES
6994005580c4   syedshah3994/hello-flask:1.0   "python app.py"   52 minutes ago      Up 36 minutes            0.0.0.0:5002->5000/tcp, [::]:5002->5000/tcp   webapp-renamed
094a00193310   syedshah3994/hello-flask:1.0   "python app.py"   About an hour ago   Up About an hour         0.0.0.0:5001->5000/tcp, [::]:5001->5000/tcp   test-app
81804e597c02   syedshah3994/hello-flask:1.0   "python app.py"   About an hour ago   Created                                                                my-app
ad176c8f65bb   hello-world                    "/hello"          2 hours ago         Exited (0) 2 hours ago                                                 cool_elion

## Verify both files persist in the volume:
PS C:\Users\syedshah\Documents\GitHub\assignment1-part3> docker run --rm -v my_volume:/data alpine ls -l /data
Unable to find image 'alpine:latest' locally
latest: Pulling from library/alpine
e2de96513ba9: Pull complete 
6d0606d1815c: Download complete 
797dd00a0fc7: Download complete 
Digest: sha256:294b683cb724975bec92580e1e685676bd4b50bda910ddb8c51d4cabeaec77e6
Status: Downloaded newer image for alpine:latest
total 12
-rw-r--r--    1 root     root           497 Sep 15 12:54 50x.html
-rwxr-xr-x    1 root     root            69 Oct  7 10:46 about.html
-rwxr-xr-x    1 root     root            70 Oct  7 10:28 index.html
PS C:\Users\syedshah\Documents\GitHub\assignment1-part3> docker run --rm -v my_volume:/data alpine cat /data/index.html /data/about.html
<html><body><h1>Hello from my_volume - index.html</h1></body></html>
<html><body><h1>About page - served from my_volume</h1></body></html>

## Cleanup:
PS C:\Users\hamza\Documents\GitHub\assignment1-part3> docker volume rm my_volume
my_volume
PS C:\Users\hamza\Documents\GitHub\assignment1-part3> docker volume ls
DRIVER    VOLUME NAME