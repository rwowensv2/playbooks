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
[Traefik](https://doc.traefik.io/traefik/)

### Traefik T2R
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


## Azure Pipelines - OLD
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

## Working...  
See subdirectories  

## K8s
[Simple](https://ralph.blog.imixs.com/2019/11/17/from-docker-swarm-to-kubernetes-in-the-easy-way/)  
[cgroup error docker driver](https://sysnet4admin.gitbook.io/k8s/trouble-shooting/cluster-build/kubelet-is-not-properly-working-on-1.22-version)  
