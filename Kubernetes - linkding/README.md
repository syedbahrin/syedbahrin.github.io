# Setup K3s nodes to deploy linkding
Macbook Air M2<br>
VMware-Fusion-13.6.4-24832108<br>
ubuntu-24.04.3-live-server-arm64.iso<br>

# VM information
| VM Name | Node status | OS | vCPU | RAM (GB) | HD (GB) | IP |
|---|---|---|---|---|---|---|
| cp01 | Control plane | Ubuntu 24.04.3 Minimal | 2 | 2 | 20 | 172.16.165.128 |
| wn01 | Worker | Ubuntu 24.04.3 Minimal | 2 | 2 | 20 | 172.16.165.129 |

# VMware Fusion main interface
![Alt text](images/VMware_Fusion_Main.png)

# VM setting
![Alt text](images/VMware_Fusion_VM_setting.png)

# Update OS and then reboot VM
```cmd
sudo apt update && sudo apt upgrade -y
```
```cmd
sudo reboot now
```
# Configure /etc/hosts on both VMs, add two hostname 
172.16.165.128 cp01<br>
172.16.165.129 wn01

# Disable swap (required by Kubernetes)
sudo swapoff -a<br>
sudo sed -i '/swap/d' /etc/fstab

# Install K3s on Master node
curl -sfL https://get.k3s.io | sh -
<br>Run below command to check node status<br>
sudo k3s kubectl get nodes

# Copy token for next step
sudo cat /var/lib/rancher/k3s/server/node-token

# Install k3s on Worker node
curl -sfL https://get.k3s.io | K3S_URL=https://<ip_master_node>:6443 K3S_TOKEN=Token_master_node sh -
<br>Run below command on control plane node to check node status<br>
sudo k3s kubectl get nodes

# Deploy linkding
Create a yaml file deploy_linkding.yaml deploy linkding<br>

```yaml
apiVersion: v1
kind: Namespace
metadata:
  name: linkding
---
apiVersion: apps/v1
kind: Deployment
metadata:
   name: linkding
   namespace: linkding
spec:
  replicas: 1
  selector:
    matchLabels:
      app: linkding
  template:
    metadata:
      labels:
        app: linkding
    spec:
      containers:
        - name: linkding
          image: sissbruecker/linkding:1.44.1
          ports:
            - containerPort: 9090 
```
Run below command to deploy linkding<br>
sudo k3s kubectl apply -f deploy_linkding.yaml<br><br>

Run below command to check linkding pod<br>
sudo k3s kubectl get pods -n linkding<br>
![Alt text](images/kuberctl_get_pods_n_linkding.png)

# Create port forward
sudo k3s kubectl port-forward pod/linkding-7d4c6f6dbb-6hbfl 9090:9090 -n linkding<br>

# Create service for linkding
Create a yaml file svc_linkding.yaml to deploy service<br>
```yaml
apiVersion: v1
kind: Service
metadata:
  name: linkding-service
  namespace: linkding
spec:
  type: LoadBalancer
  selector:
    app: linkding
  ports:
    - port: 9090
      targetPort: 9090
      protocol: TCP
      name: http
```
Run below command to deploy service<br>
sudo k3s kubectl apply -f svc_linkding.yaml<br><br>
Run below command to check service<br>
sudo k3s kubectl get svc -A<br>
![Alt text](images/kuberctl_get-svc.png)

# Setup administrator account for linkding
sudo k3s kubectl  exec -it linkding-7d4c6f6dbb-6hbfl -n linkding -- python3 manage.py createsuperuser --username=sysadmin --email=syedbahrin@example.com<br>
Enter password<br>
![Alt text](images/create_superuser-account.png)

# Access linkding page using superuser credential
![Alt text](images/enter_superuser_credential.png)

# In superuser profile page
![Alt text](images/superuser_account.png)

