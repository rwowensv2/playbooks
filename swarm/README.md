# Master initialize swarm
docker swarm init --advertise-addr 192.168.1.21
docker swarm init --advertise-addr=192.168.1.21 --listen-addr 192.168.1.21:2377 > /tmp/worker_token.txt
>>

docker swarm join --token SWMTKN-1-0vdphnzsp26o8mkbvt2ca3snzqpl7y5q8mms9hflvdycprfrwm-104ljk5loonziswobka9ohpxq 192.168.1.21:2377

>> 
# node already part of a swarm
docker swarm leave
# get token + command
docker swarm join-token worker

#
# Traefik Host Based 

# create overlay network
docker network create --driver overlay --attachable traefik-net

#
# Create traefik service
```
docker service create \
    --name traefik \
    --constraint=node.role==manager \
    --publish 80:80 --publish 8080:8080 \
    --mount type=bind,source=/var/run/docker.sock,target=/var/run/docker.sock \
    --network traefik-net \
    traefik \
    --providers.docker \
    --providers.docker.swarmMode \
    --providers.docker.domain=traefik \
    --providers.docker.watch \
    --api
```
#
# Create nginx test web
```
docker service create \
  --name nginxdev \
  --label traefik.port=80 \
  --network traefik-net \
  --label traefik.frontend.rule="Host:www.owens.dev" \
  --detach \
  nginx
```

#
# Check it
```
docker service ls
ID                  NAME                MODE                REPLICAS            IMAGE               PORTS
1vit7x7zd0xj        nginxdev            replicated          2/2                 nginx:latest        
yn703xe3quw2        traefik             replicated          1/1                 traefik:latest      *:80->80/tcp, *:8080->8080/tcp
```

#
# Scale the Service
```
docker service scale nginxdev="2"
```

#
# Check it
```
docker service ps nginxdev
ID                  NAME                IMAGE               NODE                 DESIRED STATE       CURRENT STATE           ERROR               PORTS
i9scy6y928f2        nginxdev.1          nginx:latest        centvbox.owens.dev   Running             Running 5 minutes ago                       
p66qahf1ya0h        nginxdev.2          nginx:latest        desk.owens.dev       Running             Running 2 minutes ago                       
```
```
docker service create \


# SERVICENAME=your service name you choose
# NETWORKNAME=owens
# CONTAINERIMAGE=whatever nginx, apache, etc.
# docker run --name nginxdev -v /usr/share/www:/usr/share/nginx/html:ro -d nginx
docker service create --name=<SERVICENAME> --endpoint-mode dnsrr --network=<NETWORKNAME> <CONTAINERIMAGE> [COMMAND] [ARGS…]
docker service create --name=nginxdev --endpoint-mode dnsrr --publish mode=host,target=80 --network=owens nginx -v /usr/share/www:/usr/share/nginx/html:ro -d nginx
# working
# docker service create --name=nginxdev --endpoint-mode dnsrr --publish mode=host,target=80 --network=t nginx

docker service create \
  --name nginxdev \
  --label traefik.port=80 \
  --network traefik-net \
  --label traefik.frontend.rule="Host:www.owens.dev" \
  --detach \
  nginx


docker service create \
  --name nginxdev \
  --endpoint-mode dnsrr \
  --publish mode=host,target=80 \
  --mount type=bind,source=/usr/share/www,destination=/usr/share/nginx/html \
  --network=tra \
   nginx(:alpine)
# docker service ps nginxdev (show port info)
```

# optional
```
docker node update --label-add os=something hostname
```


# Scale
DOCKER SERVICE  
docker service ls  
docker service rm <servicename>  



# Misc 
```
whoami:
    image: containous/whoami # A container that exposes an API to show its IP address
    labels:
      - "traefik.frontend.rule=Host:whoami.docker.localhost"
```

To add a worker to this swarm, run the following command:  
```
    docker swarm join --token SWMTKN-1-50hoknf19hqn26gh7n9rd2wjk64avywnpzcti9gtzxye909oea-76l71vhkndmjt6746x2l5tujc 192.168.99.107:2377
```

To add a manager to this swarm, run 'docker swarm join-token manager' and follow the instructions.  

```
docker-machine ssh worker1 "docker swarm join \
    --token=SWMTKN-1-50hoknf19hqn26gh7n9rd2wjk64avywnpzcti9gtzxye909oea-76l71vhkndmjt6746x2l5tujc \
    --listen-addr $(docker-machine ip worker1) \
    --advertise-addr $(docker-machine ip worker1) \
    $(docker-machine ip manager)"

docker-machine ssh worker2 "docker swarm join \
    --token=SWMTKN-1-50hoknf19hqn26gh7n9rd2wjk64avywnpzcti9gtzxye909oea-76l71vhkndmjt6746x2l5tujc \
    --listen-addr $(docker-machine ip worker2) \
    --advertise-addr $(docker-machine ip worker2) \
    $(docker-machine ip manager)"

docker-machine ssh manager "docker service create \
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
    --api"
```
# Compose
```
sudo docker stack deploy -c docker-compose.yml reverse-proxy
sudo docker stack ls
sudo docker stack rm reverse-proxy
```
```
docker-compose.yml 
version: '3'

services:
  reverse-proxy:
    image: traefik # The official Traefik docker image
    command: --api --docker # Enables the web UI and tells Traefik to listen to docker
    ports:
      - "80:80"     # The HTTP port
      - "8080:8080" # The Web UI (enabled by --api)
    volumes:
      - /var/run/docker.sock:/var/run/docker.sock # So that Traefik can listen to the Docker events

```
# Ansible Swarm Facts
Inspect is deprecated, use `docker_swarm_info`.  Below is an example of extracting the worker key for join.  


```
  tasks:
    - name: inspect
      docker_swarm_info:
        nodes: yes
      register: result
      # OLD Way
      # "{{ hostvars['ansible_hostname']['token']['swarm_facts']['JoinTokens']['Worker'] }}"
      when: role is defined and role == "leader"
    - debug:
        var: result.swarm_facts.JoinTokens.Worker
```

# reminder commands
```
docker node inspect  
docker node update
```



