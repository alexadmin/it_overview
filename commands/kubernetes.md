<strong>Containers operation</strong>

```
# INTER INTO CONTAINER
kubectl exec -it my-pod -- /bin/bash

# SHOW ALL
kubectl get rc,pods,deployment,daemonset,svc,ingress,secrets,configmaps --all-namespaces=true

# FORWARD PORT
kubectl port-forward -p redis-master 6379:6379

# SHOW PODS
kubectl get pods

# SHOW PODS WITH NAME
kubectl get pod my-pod

kubectl describe pods my-nginx

# DELETE RC
kubectl delete rc my-nginx

# SHOW PODS WITH NODES
kubectl get pods -o wide

# START CONTAINER
kubectl run my-nginx --image=nginx --replicas=2 --port=80

# DELETE POD
kubectl delete pod mypod

# CREATE POD
kubectl create -f my-pod.yaml

# EXECUTE IN CONTAINER
kubectl exec shell-demo ls /

# EXPOSE PORT
kubectl run my-nginx --image=nginx --replicas=1 --port=80
kubectl expose rc my-nginx --port=80 --target-port=80 --name=my-nginx-akfud --public-ip="192.168.7.27"
```

<strong>Cluster administration</strong>

```
# CHECK API SERVER
kubectl cluster-info

# SHOW LEADER
kubectl get endpoints kube-controller-manager --namespace=kube-system  -o yaml

# disconnect node
kubectl drain mynode
# connect node
kubectl uncordon mynode 
```

<strong>rc-nginx.yaml</strong>
```
apiVersion: v1
kind: ReplicationController
metadata:
  name: rc-nginx
spec:
  replicas: 2
  selector:
    app: nginx
  template:
    metadata:
      labels:
        app: nginx
    spec:
      containers:
      - name: nginx
        image: nginx:1.7.9
        ports:
        - containerPort: 80
```

<strong>svc-nginx.yaml</strong>
```
apiVersion: v1
kind: Service
metadata:
  name: nginxsvc
  labels:
    app: nginx
spec:
  type: LoadBalancer
  externalIPs:
    - 10.99.1.7
    - 10.99.1.3
  ports:
    - protocol: TCP
      port: 80
  selector:
      app: nginx
```

```
# get deployments
curl -k -H "Authorization: Bearer ${TOKEN}" https://${IP}:6443/apis/apps/v1/namespaces/default/deployments
```
