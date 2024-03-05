## Compose sample application

### Use with Docker Development Environments

You can open this sample in the Dev Environments feature of Docker Desktop version 4.12 or later.

### ASP.NET server with an Nginx proxy and a MySQL database

Project structure:
```
.
├── frontend
│   ├── Dockerfile
│   └── compose.yaml
├── backend
│   ├── Dockerfile
│   ├── aspnet.csproj
│   └── Program.cs
├── db
│   └── password.txt
├── compose.yaml
├── proxy
│   ├── conf
│   └── Dockerfile
└── README.md
```

[_compose.yaml_](compose.yaml)
```
services:
  backend:
    build:
      context: backend
    ...
  db:
    # We use a mariadb image which supports both amd64 & arm64 architecture
    image: mariadb:10-focal
    # If you really want to use MySQL, uncomment the following line
    #image: mysql:8
    ...
  proxy:
    build: proxy
    ports:
    - 80:80
    ...
```
The compose file defines an application with three services `proxy`, `backend` and `db`.
When deploying the application, docker compose maps port 80 of the proxy service container to port 80 of the host as specified in the file.
Make sure port 80 on the host is not already being in use.

> ℹ️ **_INFO_**  
> For compatibility purpose between `AMD64` and `ARM64` architecture, we use a MariaDB as database instead of MySQL.  
> You still can use the MySQL image by uncommenting the following line in the Compose file   
> `#image: mysql:8.0.27`

## Deploy with docker compose

```
$ docker compose up -d
```

## Expected result

Listing containers must show three containers running and the port mapping as below:
```
$ docker ps
CONTAINER ID   IMAGE                COMMAND                  CREATED          STATUS                    PORTS                                                                              NAMES
769684ed4b05   web-stack_proxy      "nginx -g 'daemon of…"   32 seconds ago   Up 31 seconds             80/tcp, 0.0.0.0:81->81/tcp, :::81->81/tcp                                          web-stack_proxy_1
98332efa8fe5   web-stack_backend    "dotnet aspnetapp.dll"   32 seconds ago   Up 32 seconds                                                                                                net-backend
1c20e5b847bc   mariadb:10-focal     "docker-entrypoint.s…"   40 seconds ago   Up 39 seconds (healthy)   3306/tcp                                                                           web-stack_db_1
9eff74ff17c2   web-stack_frontend   "docker-entrypoint.s…"   40 seconds ago   Up 39 seconds             0.0.0.0:3000->3000/tcp, :::3000->3000/tcp, 0.0.0.0:80->3000/tcp, :::80->3000/tcp   react-ui

```

After the application starts, 

To access the frontend, navigate to `http://localhost:80` in your browser:
![image](https://github.com/NarglesCS/web-stack/assets/24685640/d99ef99a-5152-49f9-b3da-192c79445206)

To access the backend, navigate to `http://localhost:81` in your web browser or run:
```
$ curl localhost:81
["Blog post #0","Blog post #1","Blog post #2","Blog post #3","Blog post #4"]
```

Stop and remove the containers
```
$ docker compose down
```
