# playbooks
Misc Playbooks

## Common
basic env build

Example `internal.yaml` 
```
gourl: http://localhost:8080/custom/go/go1.12.7.linux-amd64.tar.gz
luaurl: http://localhost:8080/custom/lua/lua-5.3.5.tar.gz
haurl: http://localhost:8080/custom/haproxy/haproxy-2.0.3.tar.gz
confdurl: http://localhost:8080/custom/confd/confd-0.16.0-linux-amd64
etcdurl: http://localhost:8080/custom/etcd/etcd-v3.3.13-linux-amd64.tar.gz
terraurl: http://localhost:8080/custom/terraform/terraform_0.12.5_linux_amd64.zip
dockerurl: http://localhost:8080/custom/docker
composeurl: https://localhost:8080/custom/docker

```

## Ansible Swarm
Python Dephole

```
sudo yum install epel-release

yum install python2-pip

pip install --upgrade pip

pip uninstall docker-py

pip install docker-py
```

## swarm.yml
Inventory vars example, and hostvar expl.
```
[swarm]
desk.owens.dev role=leader
centvbox.owens.dev	
vbox2.owens.dev		
```

# Traefik
```
#
# Traefik Host Based 

# create overlay network
docker network create --driver overlay --attachable traefik-net

#
# Create traefik service
sudo docker service create \
    --name traefik \
    --constraint=node.role==manager \
    --publish 80:80 --publish 8080:8080 \
    --mount type=bind,source=/var/run/docker.sock,target=/var/run/docker.sock \
    --network traefik-net \
    traefik \
    --docker \
    --docker.swarmMode \
    --docker.domain=traefik \
    --docker.watch \
    --api

# Service Tests

## Create nginx basic service
sudo docker service create \
  --name nginxdev \
  --label traefik.port=80 \
  --network traefik-net \
  --label traefik.frontend.rule="Host:www.owens.dev" \
  --detach \
  nginx

## Nginx Test service prints host (nginxhtml.yml)
sudo docker service create \
  --name nginxdev \
  --mount type=bind,source=/usr/share/www/,destination=/usr/share/nginx/html/ \
  --label traefik.port=80 \
  --network traefik-net \
  --label traefik.frontend.rule="Host:www.owens.dev" \
  --detach \
  nginx

## Nginx Test Pathstrip http://mysite.com/test/ will route to Nginx:/
sudo docker service create \
  --name nginxpath \
  --label traefik.port=80 \
  --network traefik-net \
  --label traefik.frontend.rule="PathStrip:/test" \
  --detach \
  nginx



#
# Check it
sudo docker service ls
ID                  NAME                MODE                REPLICAS            IMAGE               PORTS
1vit7x7zd0xj        nginxdev            replicated          2/2                 nginx:latest        
yn703xe3quw2        traefik             replicated          1/1                 traefik:latest      *:80->80/tcp, *:8080->8080/tcp


#
# Scale the Service
sudo docker service scale nginxdev="2"

#
# Check it
docker service ps nginxdev
ID                  NAME                IMAGE               NODE                 DESIRED STATE       CURRENT STATE           ERROR               PORTS
i9scy6y928f2        nginxdev.1          nginx:latest        centvbox.owens.dev   Running             Running 5 minutes ago                       
p66qahf1ya0h        nginxdev.2          nginx:latest        desk.owens.dev       Running             Running 2 minutes ago                       
```
### Traefik HealthCheck
```
docker service update --label-add traefik.backend.healthcheck.path=/nginx traefik
docker service update --label-add traefik.backend.healthcheck.interval=5s traefik
```

# Portainer
[Portainer Getting Started](https://www.portainer.io/installation/)  
[Portainer Documentation](https://portainer.readthedocs.io/en/stable/index.html)  

## Quick Start
```
docker volume create portainer_data

docker run -d -p 8000:8000 -p 9000:9000 -v /var/run/docker.sock:/var/run/docker.sock -v portainer_data:/data portainer/portainer
```

# etcd
[Cluster](https://github.com/etcd-io/etcd/blob/master/Documentation/op-guide/clustering.md) 


## Azure Pipelines
Figured out so far :-)  

Creating a Azure Devops Account, be prepared to link to an existing GitHub Account (use your Github creds for Azure) and Create a project to link to existing repo.  I linked to Azure to test docker-compose stuff.  

`dotnet.yaml` for installing agent prereq's and creating user.  Think gitlab-runner.  

* Azure Devops Account  
* Create Access Token (copy n paste, vault it!)  
* Create Deployment Group:  Pipelines > Deployment Group (use this name for the runner)  
* Run `dotnet.yaml` to install prereq packages and create the user.
* Goto your deployment group and select "Details", to get script (Target to Register)  
* Run the script as the user. Had to give user temp sudo and revoke (gotta figure out a cleaner way)  

 Note:  Script will prompt for the "Deployment Group" name you created, and "PAT" auth token you created!  

Magic.  Your on-prem host will appear in the Deployment Group.  Next to work up a gitlab-ci.. err, azure-pipeline.yaml :-)  

## Docker Images - No Inet Access
Example of getting images on a system with no Internet Access, and no local registry (yet).  
  
  
On system with Inet Access, get the image:  
```docker pull portainer/portainer```  
save the image to a tarball:  
```docker image save -o portainer.tar```  
Copy the image to remote host (no dockerhub access), and load the image:  
```docker load -i portainer.tar```  