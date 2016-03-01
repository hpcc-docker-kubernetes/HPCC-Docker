

## HPCC-Docker Images and Simple Deployment

Refernce https://hub.docker.com/r/hpccsystems/platform-ce/ for HPCC Docker images usage

## Prerequisites
Install [Docker](https://docs.docker.com/engine/installation/) and [Docker-compose](https://docs.docker.com/compose/install/)
Before you continue following steps make sure docker machine, for example if you use Virtualbox, or docker daemon, for example on native Linux, is running.

## Deploy a HPCC Cluster with Docker Compose
The default docker compose configuration file docker-compose.yml defines three servcies: hpcc-master, 
hpcc-roxie and hpcc-thor. hpcc-master will host dali, esp, dfuserver, etc. The default HPCC image is for Ubuntu Trusty.
You can change to other supplied [docker hpcc images](https://hub.docker.com/r/hpccsystems/platform-ce/tags/) or build a new one yourself.

### Start HPCC cluster 
Download HPCC-Docker.git:
```sh
git clone https://github.com/xwang2713/HPCC-Docker.git
```
The default repository directory is HPCC-Docker
Go to HPCC-Docker directory and run: 
```sh
docker-compose up -d
Creating hpccdocker_hpcc-roxie_1
Creating hpccdocker_hpcc-thor_1
Creating hpccdocker_hpcc-master_1
```
The default docker-compose.yaml define one thor slave and one roxie instance.
To check the cluster status and make sure state is "UP" for each container: 
```sh
docker-compose ps
          Name                        Command               State                          Ports                         
------------------------------------------------------------------------------------------------------------------------
hpccdocker_hpcc-master_1   bash -c cd /tmp; /tmp/run_ ...   Up      8002/tcp, 0.0.0.0:8010->8010/tcp, 8015/tcp, 9876/tcp 
hpccdocker_hpcc-roxie_1    bash -c sudo /usr/sbin/sshd -D   Up      8002/tcp, 8010/tcp, 8015/tcp, 9876/tcp               
hpccdocker_hpcc-thor_1     bash -c sudo /usr/sbin/sshd -D   Up      8002/tcp, 8010/tcp, 8015/tcp, 9876/tcp  
```
Find host ip if you use VM as host, otherwise the host ip can be localhost or real ip address:
```sh
docker-machine ls
NAME      ACTIVE   DRIVER       STATE     URL                         SWARM   DOCKER   ERRORS
default   *        virtualbox   Running   tcp://192.168.99.100:2376           v1.9.1   

```
To verify and play with HPCC Platform: http://<host ip>:8010. For example http://192.168.99.100:8010
### Scale thor and roxie instances
To add new thor or roxie use:
```sh
docker-compose scale <service name to add>=<number instance total>
```
For example to make total three thor containter (each container has one slave): 
```sh
docker-compose scale hpcc-thor=2
Creating and starting 2 ... done
```
You need delete/re-create hpcc-master for this new configuration:
```sh
docker-compose stop hpcc-master
Stopping hpccdocker_hpcc-master_1 ... done

docker-compose rm hpcc-master
Going to remove hpccdocker_hpcc-master_1
Are you sure? [yN] y
Removing hpccdocker_hpcc-master_1 ... done

docker-compose run -d -p 8010:8010 hpcc-master
hpccdocker_hpcc-master_run_1
```

On CentOS you may need login to hpcc-master to manually start the cluser:
1. Find hpcc-master container id (first column): ```docker ps | grep hpcc-master```
2. Login interactive shell: ```docker exec -i -t --privileged -e "container=docker" <id> /bin/bash```
3. Start HPCC cluster: ```sudo /opt/HPCCSystems/sbin/hpcc-run start```.
Eventhough login shell is for root but "USER" variable is not defined which is why we need use "sudo" here. We will fix with id instead USER varialbe in future hpcc-run.sh code.

### Stop the cluster:
```sh
docker-compose kill
Killing hpccdocker_hpcc-thor_2 ... done
Killing hpccdocker_hpcc-thor_1 ... done
Killing hpccdocker_hpcc-roxie_1 ... done

docker-compose rm (type 'y' when prompt)
Going to remove hpccdocker_hpcc-thor_2, hpccdocker_hpcc-thor_1, hpccdocker_hpcc-roxie_1
Are you sure? [yN]y
Removing hpccdocker_hpcc-thor_2 ... done
Removing hpccdocker_hpcc-thor_1 ... done
Removing hpccdocker_hpcc-roxie_1 ... done
```
If restart hpcc-master you need run docker commands to stop and remove it:
```sh
docker ps
e1324676a4cf        hpccsystems/platform-ce:5.4.8-1trusty   "bash -c 'cd /tmp; /t"   7 minutes ago       Up 7 minutes 

docker stop e1324676a4cf
e1324676a4cf

docker rm  e1324676a4cf
e1324676a4cf
```
There is help a script 'clean_run.sh" can do it but just remmeber it will remove all containers.
       
        

## Build HPCC Docker images

Checkout this git repository:
```sh
git clone https://github.com/xwang2713/HPCC-Docker.git
```
Check if the image already exist locally or not:
```sh
docker images
```
If the "REPOSITORY" and 'TAG' fields are the same as the one you want to build, you need remove existing one firs:
```sh
docker rmi <IMAGE ID>
```
Create build directory, for example build and go inside of it.
Depends on which you want to build (Ubuntu Trusty or CentOS 7 and HPCC version), you can use help script
under HPCC-Docker or modify them or call HPCC-Docker/build.sh. For example to build HPCC 5.4.8-1 on Trusty:
```sh
../HPCC-Docker/build_trusty.sh
```
If every build run OK build output will display "successfully ....". 
docker images will show:
```sh
docker images
```

