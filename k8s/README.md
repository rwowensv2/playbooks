# K8s

## Commands
```
kubectl version
kubectl get nodes
kubectl create deployment
kubectl get deployments
...
kubectl proxy
  curl http://localhost:8001/

export POD_NAME=$(kubectl get pods -o go-template --template '{{range .items}}{{.metadata.name}}{{"\n"}}{{end}}')
```

## More
```
$ kubectl get -n default services
NAME         TYPE           CLUSTER-IP     EXTERNAL-IP   PORT(S)                      AGE
kubernetes   ClusterIP      10.96.0.1      <none>        443/TCP                      3h53m
traefik      LoadBalancer   10.96.24.117   <pending>     80:30293/TCP,443:32127/TCP   140m

$ kubectl patch svc traefik -p '{"spec":{"externalIPs":["192.168.1.201"]}}'
||?
$ kubectl patch svc traefik -p '{"spec":{"LoadBalancer":{"externalIPs":["192.168.1.201"]}}}'

$ kubectl get -n default services
NAME         TYPE           CLUSTER-IP     EXTERNAL-IP     PORT(S)                      AGE
kubernetes   ClusterIP      10.96.0.1      <none>          443/TCP                      3h56m
traefik      LoadBalancer   10.96.24.117   192.168.1.201   80:30293/TCP,443:32127/TCP   142m

$ kubectl apply -f dashboard.yaml
```

# Reset  
Should the need arise to start from scratch.  

## On all nodes:  
```
# Reset the node
sudo kubeadm reset
# Pull images ahead of time
sudo kubeadm config images pull
```
## On Leader:
```
sudo kubeadm init --pod-network-cidr=10.244.0.0/16 --apiserver-advertise-address=<ipaddr>
```
## ON Nodes:
Use the join command, or if you forgot:  
```
sudo kubeadm token create --print-join-command
```
## Check
```
$ sudo kubectl get nodes -o wide
NAME              STATUS   ROLES                  AGE   VERSION   INTERNAL-IP     EXTERNAL-IP   OS-IMAGE                KERNEL-VERSION                CONTAINER-RUNTIME
vbox1.owens.dev   Ready    control-plane,master   11m   v1.22.1   192.168.1.201   <none>        CentOS Linux 7 (Core)   3.10.0-1160.36.2.el7.x86_64   docker://20.10.8
vbox2.owens.dev   Ready    <none>                 11m   v1.22.1   192.168.1.202   <none>        CentOS Linux 7 (Core)   3.10.0-1160.36.2.el7.x86_64   docker://20.10.8
vbox3.owens.dev   Ready    <none>                 14s   v1.22.1   192.168.1.203   <none>        CentOS Linux 7 (Core)   3.10.0-1160.36.2.el7.x86_64   docker://20.10.8
```