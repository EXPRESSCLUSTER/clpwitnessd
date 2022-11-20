# How to Deploy clpwitnessd
- clpwitnesd is a module of CLUSTERPRO/EXPRESSCLUSTER to prevent split-brain.

## Index
- [Sample Configuration](#sample-configuration)
- [How to Deploy clpwitnessd](#how-to-deploy-clpwitnessd)
  - [Linux](#linux)
  - [Docker](#docker)
  - [Kubernetes](#kubernetes)
  - [Azure App Service](#azure-app-serivce)

## Sample Configuration
```
            +-----------------+
            | clpwitnessd     |
            | 192.168.1.1/24  |
            +--------+--------+
                     |
         +-----------+-----------+
         |                       |
+--------+--------+     +--------+--------+
| server1         |     | server2         |
| 192.168.1.11/24 |     | 192.168.1.12/24 |
|                 |     |                 |
| 192.168.0.11/24 |     | 192.168.0.12/24 |
+--------+--------+     +--------+--------+
         |                       |
         +-----------------------+
```
- Heartbeat I/F Priority List
  |Priority|Type       |MDC       |server1     |server2     |
  |--------|-----------|----------|------------|------------|
  |       1|Kernel Mode|mdc1      |192.168.0.11|192.168.0.12|
  |       2|Kernel Mode|Do Not Use|192.168.1.11|192.168.1.12|
  |       3|Witness    |Do Not Use|Use         |Use         |

- Witness Heartbeat Properties (Linux, Docker)
  - Target Host: 192.168.1.1
  - Service Port: 8080

- Witness Heartbeat Properties (Kubernetes)
  - Target Host: 192.168.1.1
  - Service Port: 30080


## How to Deploy clpwitnessd
### Linux
1. Install Node.js on a Linux machine.
1. Copy the clpwitnessd module from CLUSTERPRO/EXPRESSCLUSTER Media to the Linux machine. If your DVD drive name is D:, you can find the module on the following directory.
   - D:\Common\5.0\common\tools\witnessd\clpwitnessd-5.0.0.tgz
1. Install clpwitnesd.
   ```sh
   npm install --global clpwitnessd-5.0.0.tgz
   ```
1. Check if the clpwitnessd is installed.
   ```sh
   npm list --global clpwitnessd
   ```
   - Command result will be as below.
     ```
     /usr/lib
     `-- clpwitnessd@5.0.0
     ```
1. Run clpwitnessd.
   ```sh
   clpwitnessd
   ```
<!--
### Enable HTTPS for clpwitnessd
-->
### Docker
1. Install Docker on a Linux machine.
1. Pull the clpwitnessd container image from Docker Hub.
   ```sh
   docker pull expresscluster/clpwitnessd:latest
   ```
   - EXPRESSCLUSTER Docker Hub
     - https://hub.docker.com/r/expresscluster/clpwitnessd
1. Run the containr.
   ```sh
   docker run -it -d --name clpwitnessd -p 8089:8080 docker.io/expresscluster/clpwitnessd:latest clpwitnessd:latest
   ```     

### Kubernetes
1. Create a Kubernetes cluster on Linux machines.
1. Create a yaml file (e.g. clpwitnessd.yml)as below.
   ```yaml
   apiVersion: v1
   kind: Pod
   metadata:
     name: clpwitnessd
     labels:
       app: clpwitnessd
   spec:
     containers:
     - name: clpwitnessd
       image: docker.io/expresscluster/clpwitnessd:latest
       ports:
       - containerPort: 8080
   ---
   apiVersion: v1
   kind: Service
   metadata:
     name: clpwitnessd
   spec:
     type: NodePort
     ports:
       - name: clpwitnessd
         protocol: TCP
         port: 8080
         targetPort: 8080
         nodePort: 30080
     selector:
       app: clpwitnessd   
   ```
1. Apply the yaml file.
   ```sh
   kubectl apply -f clpwitnessd.yml
   ```

### Azure App Serivce
- Refer to [App Service](https://github.com/EXPRESSCLUSTER/App-Service) repository.
