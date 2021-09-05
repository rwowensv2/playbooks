# K8s
```
minikube start --driver=virtualbox
```

## Commands
```
    kubectl get - list resources
    kubectl describe - show detailed information about a resource
    kubectl logs - print the logs from a container in a pod
    kubectl exec - execute a command on a container in a pod
```

```
kubectl version
kubectl get all
kubectl get pods
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

$ kubectl get -n default services
NAME         TYPE           CLUSTER-IP     EXTERNAL-IP     PORT(S)                      AGE
kubernetes   ClusterIP      10.96.0.1      <none>          443/TCP                      3h56m
traefik      LoadBalancer   10.96.24.117   192.168.1.201   80:30293/TCP,443:32127/TCP   142m

$ kubectl apply -f dashboard.yaml
```
