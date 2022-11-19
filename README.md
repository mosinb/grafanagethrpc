
# GETH RPC Monitoring with Prometheus & Grafana

This is a Docker compose document that leverages existing docker images for Geth RPC monitoring using Prometheus and Grafana

### Grafana Application with Prometheus

Project structure:
```
.
├── grafana
│   ├── dashboards
│   └── datasources
├── .env-example
├── docker-compose.yml
├── prometheus.yml
└── README.md
```

[_docker-compose.yml_](docker-compose.yml)
```
services:
version: "3.9"
services:
  rpcexporter:
    image: hunterlong/gethexporter
    container_name: rpcexporter
    env_file: .env
  prometheus:
    image: prom/prometheus
    container_name: prometheus
    ...
  grafana:
    image: grafana/grafana
    container_name: grafana
    ...
```
The compose file defines an application with three services `rpcexporter`, `prometheus`, and `grafana`.
When deploying the application, docker compose maps port 3000 of the backend service container to port 8080 of the host as specified in the file.
Make sure port 3000 on the host is not already being in use.

## Edit .env file
```
$ docker-compose up -d
cp .env-example .env
```

[_.env_](.env)
```
# Set RPC Server Address
GETH="http://127.0.0.1:8575"
```

## Deploy with docker compose

```
$ docker-compose up -d
[+] Running 4/4
 ⠿ Network grafanagethrpc_default  Created                                                                                                                                                0.1s
 ⠿ Container rpcexporter           Started                                                                                                                                                0.5s
 ⠿ Container prometheus            Started                                                                                                                                                0.7s
 ⠿ Container grafana               Started   
```

## Expected result

Listing containers must show two containers running and the port mapping as below:
```
$ docker ps
CONTAINER ID   IMAGE                     COMMAND                  CREATED          STATUS         PORTS                    NAMES
8972df82f144   grafana/grafana           "/run.sh"                10 seconds ago   Up 9 seconds   0.0.0.0:8080->3000/tcp   grafana
dfd70436e29b   prom/prometheus           "/bin/prometheus --c…"   10 seconds ago   Up 9 seconds   9090/tcp                 prometheus
96bf3513e230   hunterlong/gethexporter   "/bin/sh -c gethexpo…"   10 seconds ago   Up 9 seconds   9090/tcp                 rpcexporter
```

After the application starts, open on browser the following link:

http://localhost:8080/d/FDX6YtOiz/geth-server

> ℹ️ **_INFO_**  
> Grafana's default username/password:  admin/admin

## Screenshots


![Login](/screenshots/grafanalogin.png?raw=true "Grafana Login")
![Dashboard](/screenshots/dashboard.png?raw=true "Dashboard")

Stop and remove the containers
```
$ docker compose down
[+] Running 4/4
 ⠿ Container grafana               Removed                                                                                                                                                0.4s
 ⠿ Container prometheus            Removed                                                                                                                                                0.2s
 ⠿ Container rpcexporter           Removed                                                                                                                                                0.2s
 ⠿ Network grafanagethrpc_default  Removed     
```
