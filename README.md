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
docker network create --driver=overlay traefik-net

#
# Create traefik service
docker service create \
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

#
# Create nginx test web
docker service create \
  --name nginxdev \
  --label traefik.port=80 \
  --network traefik-net \
  --label traefik.frontend.rule="Host:www.owens.dev" \
  --detach \
  nginx

#
# Check it
docker service ls
ID                  NAME                MODE                REPLICAS            IMAGE               PORTS
1vit7x7zd0xj        nginxdev            replicated          2/2                 nginx:latest        
yn703xe3quw2        traefik             replicated          1/1                 traefik:latest      *:80->80/tcp, *:8080->8080/tcp


#
# Scale the Service
docker service scale nginxdev="2"

#
# Check it
docker service ps nginxdev
ID                  NAME                IMAGE               NODE                 DESIRED STATE       CURRENT STATE           ERROR               PORTS
i9scy6y928f2        nginxdev.1          nginx:latest        centvbox.owens.dev   Running             Running 5 minutes ago                       
p66qahf1ya0h        nginxdev.2          nginx:latest        desk.owens.dev       Running             Running 2 minutes ago                       
```
https://www.youtube.com/watch?v=Z3S2gMBUkBo
