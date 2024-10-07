//Notes for K3s Installation
# Deploy a Kubernetes Cluster with K3S
Please check out the official K3S Doccumentation for more detailed instructions

## Install the server
Login to the server node and install K3S

```bash
$ ssh srvadmin@ISA-K3S-01

# Make sure your nodes have unique hostnames, set the hostname if your nodes are all named localhost or something

$ hostnamectl set-hostname ISA-K3S-01
$ echo ISA-K3S-01 > /etc/hostname

# Install K3S using the install script
$ curl -sfL https://get.k3s.io | sh -
```
> After Installation check for node
```bash
# Make sure you have the /usr/local/bin directory in your path to directly access the installed binaries

$ export PATH=/usr/local/bin:$PATH
$ k3s kubectl get nodes

srvadmin@ISA-K3S-01:~$ sudo k3s kubectl get nodes
NAME         STATUS   ROLES                  AGE     VERSION
isa-k3s-01   Ready    control-plane,master   4m35s   v1.30.5+k3s1
srvadmin@ISA-K3S-01:~$ 
```
> **Export kubeconfig and set alias to avoid sudo**
```


srvadmin@ISA-K3S-01:~$ mkdir .kube
srvadmin@ISA-K3S-01:~$ sudo cp /etc/rancher/k3s/k3s.yaml .kube/config
srvadmin@ISA-K3S-01:~$ sudo chown srvadmin:srvadmin .kube/config 

srvadmin@ISA-K3S-01:~$ export KUBECONFIG=.kube/config 

srvadmin@ISA-K3S-01:~$ k3s kubectl get nodes
NAME         STATUS   ROLES                  AGE   VERSION
isa-k3s-01   Ready    control-plane,master   13m   v1.30.5+k3s1
```
## Deploy test app
```bash
kubectl apply -f web-app.yml

# Verify
kubectl get pods,svc
kubectl -n kube-system get ds
kubectl -n kube-system get pods
```
### Output

> **srvadmin@ISA-K3S-01:~$ kubectl get pods,svc**
```
NAME                           READY   STATUS    RESTARTS   AGE
pod/web-app-85498768cc-pnd6r   1/1     Running   0          10s

NAME                 TYPE           CLUSTER-IP    EXTERNAL-IP   PORT(S)        AGE
service/kubernetes   ClusterIP      10.43.0.1     <none>        443/TCP        16m
service/web-app      LoadBalancer   10.43.28.69   <pending>     80:31662/TCP   10s
```
> **srvadmin@ISA-K3S-01:~$ kubectl -n kube-system get ds**
```
NAME                     DESIRED   CURRENT   READY   UP-TO-DATE   AVAILABLE   NODE SELECTOR   AGE
svclb-traefik-2218ee74   1         1         1       1            1           <none>          16m
svclb-web-app-2ace0155   1         1         0       1            0           <none>          18s
```
> **srvadmin@ISA-K3S-01:~$ kubectl -n kube-system get pods**
```
NAME                                      READY   STATUS      RESTARTS   AGE
coredns-7b98449c4-wfz52                   1/1     Running     0          16m
helm-install-traefik-crd-wld62            0/1     Completed   0          16m
helm-install-traefik-kq8lw                0/1     Completed   1          16m
local-path-provisioner-6795b5f9d8-rwppx   1/1     Running     0          16m
metrics-server-cdcc87586-kzcl2            1/1     Running     0          16m
svclb-traefik-2218ee74-knwvj              2/2     Running     0          16m
svclb-web-app-2ace0155-x4xr4              0/1     Pending     0          26s
traefik-67f6c94c47-ddh25                  1/1     Running     0          16m
srvadmin@ISA-K3S-01:~$
```


> **Note:** svc for webapp will stay in pending since we are not using any loadbalancer yet

## Customise the server configuration

```
# Create the configuration file
# if you dont have permissions use nano or vi with sudo and create this file
cat <<EOF > /etc/rancher/k3s/config.yaml
cluster-init: true
EOF

# Restart k3s
sudo systemctl restart k3s

kubectl get nodes
```


```markdown
# Notes for K3s Installation

## Deploy a Kubernetes Cluster with K3S
Please check out the official K3S Documentation for more detailed instructions.

## Install the Server
Login to the server node and install K3S:

```bash
$ ssh srvadmin@ISA-K3S-01

# Make sure your nodes have unique hostnames, set the hostname if your nodes are all named localhost or something
$ hostnamectl set-hostname ISA-K3S-01
$ echo ISA-K3S-01 > /etc/hostname

# Install K3S using the install script
$ curl -sfL https://get.k3s.io | sh -
```

> After installation, check for the node:

```bash
# Make sure you have the /usr/local/bin directory in your path to directly access the installed binaries
$ export PATH=/usr/local/bin:$PATH
$ k3s kubectl get nodes

srvadmin@ISA-K3S-01:~$ sudo k3s kubectl get nodes
NAME        STATUS   ROLES                 AGE    VERSION
isa-k3s-01  Ready    control-plane,master  4m35s  v1.30.5+k3s1
srvadmin@ISA-K3S-01:~$
```

> **Export kubeconfig and set alias to avoid sudo:**

```bash
srvadmin@ISA-K3S-01:~$ mkdir .kube
srvadmin@ISA-K3S-01:~$ sudo cp /etc/rancher/k3s/k3s.yaml .kube/config
srvadmin@ISA-K3S-01:~$ sudo chown srvadmin:srvadmin .kube/config

srvadmin@ISA-K3S-01:~$ export KUBECONFIG=.kube/config

srvadmin@ISA-K3S-01:~$ k3s kubectl get nodes
NAME        STATUS   ROLES                 AGE   VERSION
isa-k3s-01  Ready    control-plane,master  13m   v1.30.5+k3s1
```

## Deploy Test App

```bash
kubectl apply -f web-app.yml

# Verify
kubectl get pods,svc
kubectl -n kube-system get ds
kubectl -n kube-system get pods
```

### Output

> **srvadmin@ISA-K3S-01:~$ kubectl get pods,svc:**

```bash
NAME                         READY   STATUS    RESTARTS   AGE
pod/web-app-85498768cc-pnd6r  1/1     Running   0          10s

NAME            TYPE          CLUSTER-IP   EXTERNAL-IP  PORT(S)        AGE
service/kubernetes  ClusterIP  10.43.0.1   <none>       443/TCP        16m
service/web-app     LoadBalancer  10.43.28.69  <pending>  80:31662/TCP  10s
```

> **srvadmin@ISA-K3S-01:~$ kubectl -n kube-system get ds:**

```bash
NAME                    DESIRED   CURRENT   READY   UP-TO-DATE   AVAILABLE   NODE SELECTOR   AGE
svclb-traefik-2218ee74  1         1         1       1            1           <none>          16m
svclb-web-app-2ace0155  1         1         0       1            0           <none>          18s
```

> **srvadmin@ISA-K3S-01:~$ kubectl -n kube-system get pods:**

```bash
NAME                                READY   STATUS      RESTARTS   AGE
coredns-7b98449c4-wfz52             1/1     Running     0          16m
helm-install-traefik-crd-wld62      0/1     Completed   0          16m
helm-install-traefik-kq8lw          0/1     Completed   1          16m
local-path-provisioner-6795b5f9d8   1/1     Running     0          16m
metrics-server-cdcc87586-kzcl2      1/1     Running     0          16m
svclb-traefik-2218ee74-knwvj        2/2     Running     0          16m
svclb-web-app-2ace0155-x4xr4        0/1     Pending     0          26s
traefik-67f6c94c47-ddh25            1/1     Running     0          16m
srvadmin@ISA-K3S-01:~$
```

> **Note:** The `svc` for `web-app` will stay in pending since we are not using any load balancer yet.

## Customize the Server Configuration

```bash
# Create the configuration file
# If you don't have permissions, use nano or vi with sudo and create this file
cat <<EOF > /etc/rancher/k3s/config.yaml
cluster-init: true
EOF

# Restart k3s

srvadmin@ISA-K3S-01:~$ sudo systemctl restart k3s
srvadmin@ISA-K3S-01:~$ kubectl get nodes
NAME         STATUS     ROLES                       AGE   VERSION
isa-k3s-01   NotReady   control-plane,etcd,master   37m   v1.30.5+k3s1
srvadmin@ISA-K3S-01:~$ 
```
