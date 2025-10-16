# Setup K3s to deploy linkding
Macbook Air M2<br>
VMware Fusion<br>
K3s with 2 nodes
# VM information
| VM Name | Node status | OS | vCPU | RAM (GB) | HD (GB) | IP |
|---|---|---|---|---|---|---|
| cp01 | Control plane | Ubuntu 24.04.3 Minimal | 2 | 2 | 20 | 172.16.165.128 |
| wn01 | Worker | Ubuntu 24.04.3 Minimal | 2 | 2 | 20 | 172.16.165.129 |

# VMware Fusion main interface
![Alt text](images/VMware_Fusion_Main.png)

# VM setting
![Alt text](images/VMware_Fusion_VM_setting.png)

# Update OS
sudo apt update && sudo apt upgrade -y
sudo reboot now

# Configure /etc/hosts, add two hostname 
172.16.165.128 cp01
172.16.165.129 wn01

# Disable swap (required by Kubernetes)
sudo swapoff -a
sudo sed -i '/swap/d' /etc/fstab
