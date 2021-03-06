<strong>Containers operation</strong>

```
# INTER INTO CONTAINER
kubectl exec -it my-pod -- /bin/bash

#
kubectl apply -f https://k8s.io/examples/controllers/nginx-deployment.yaml

# SHOW ALL
kubectl get rc,pods,deployment,daemonset,svc,ingress,secrets,configmaps --all-namespaces=true

# FORWARD PORT
kubectl port-forward -p redis-master 6379:6379

# SHOW PODS
kubectl get pods

# SHOW PODS WITH NAME
kubectl get pod my-pod

kubectl describe pods my-nginx

kubectl logs my-pod

# DELETE RC
kubectl delete rc my-nginx

# SHOW CONFIG
kubectl config view

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

kubectl get Role -n kube-system
kubectl get RoleBinding -n kube-system

kubectl get ClusterRoles -n kube-system
kubectl get ClusterRoleBinding -n kube-system
```

<strong>Cluster administration</strong>
```
# CHECK API SERVER
kubectl cluster-info

# JOIN
kubeadm token create --print-join-command

# SHOW LEADER
kubectl get endpoints kube-controller-manager --namespace=kube-system  -o yaml

# RESET
kubeadm reset
etcdctl del "" --prefix
```

<strong>nodes</strong>
```
# disconnect node
kubectl drain mynode
# connect back node
kubectl uncordon mynode 
# prevent a node from scheduling new pods
kubectl cordon mynode
# remove
kubectl drain <node-name> --ignore-daemonsets --delete-local-data
kubectl delete node <node-name>
```

<strong>deployment.yaml</strong>
```
apiVersion: apps/v1
kind: Deployment
metadata:
  name: nginx-deployment
  labels:
    app: nginx
spec:
  replicas: 5
  strategy:
    type: RollingUpdate
    rollingUpdate:
      maxSurge: 1        
      maxUnavailable: 0
  selector:
    matchLabels:
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
  ports:
    - protocol: TCP
      port: 80
  selector:
      app: nginx
```

<strong>ingress.yaml</strong>
```
apiVersion: extensions/v1beta1
kind: Ingress
metadata:
  name: traefik-ingr
spec:
  rules:
    - host: nginx
      http:
        paths:
          - path: /
            backend:
              serviceName: nginxsvc
              servicePort: 80
```

```
# get deployments
curl -k -H "Authorization: Bearer ${TOKEN}" https://${IP}:6443/apis/apps/v1/namespaces/default/deployments

# Dashboard admin
kubectl create serviceaccount k8sadmin -n kube-system
kubectl create clusterrolebinding k8sadmin --clusterrole=cluster-admin --serviceaccount=kube-system:k8sadmin
kubectl -n kube-system describe secret $(sudo kubectl -n kube-system get secret | (grep k8sadmin || echo "$_") | awk '{print $1}') | grep token: | awk '{print $2}'
```

### Description

##### Server:
1) kube-controller-manager
- certificate-issuing control loop
2) kube-apiserver
3) kube-scheduler

##### Worker:
1) kubelet: the component that runs on all of the machines in your cluster and does things like starting pods and containers. The kubelet doesn’t manage containers which were not created by Kubernetes.
2) kube-proxy

##### Tools:
1) kubectl: the command line util to talk to your cluster
2) kubeadm: the command to bootstrap the cluster

Authentication:

2 user types:
- service accounts, managed by the Kubernetes API, bound to specific namespaces. Service accounts are tied to "secrets"
- normal users, managed by external entity. Kubernetes does not have objects which represent normal user accounts. Normal users cannot be added to a cluster through an API call.

Role always sets permissions within a particular namespace.
ClusterRole is a non-namespaced resource.

# Deployment

### liveness 
probes to know when to restart a container

### readiness 
probes to decide when the container is available for accepting traffic.



