Homelab - Kubernetes:

Step by step guide for setting up a highly available Kubernetes cluster on VMware Workstation 17.6.4 using Rocky Linux 9.6, with HAProxy, Keepalived, VyOS, BIND, and Cilium.

System Overview

############################################################################

Virtualization Platform: VMware Workstation 17.6

Operating System (K8s Nodes): Rocky Linux 9.6 minimal installation

Router OS: VyOS Stream 1.5-2025-Q2

Network Subnet: 192.168.200.0/24

Domain: k8s.com

DNS Server: Rocky Linux 9.6 (BIND) on 192.168.200.6

Container Network Interface (CNI): Cilium

############################################################################

Router (VyOS) HA:

VyOS routers with eth0 as WAN (DHCP) and eth1 as LAN static IP.

k8s-vyos-01: eth0(bridge): dhcp, eth1(VMnet10): 192.168.200.2

k8s-vyos-02: eth0(bridge): dhcp, eth1(VMnet10): 192.168.200.3

VIP: 192.168.200.4

############################################################################

Load Balancer (HAProxy/Keepalived) HA:

k8s-lb-01.k8s.com: 192.168.200.10

k8s-lb-02.k8s.com: 192.168.200.11

VIP: 192.168.200.200

############################################################################

Kubernetes Control Plane Nodes:

k8s-cp-01.k8s.com: 192.168.200.20

k8s-cp-02.k8s.com: 192.168.200.21

k8s-cp-03.k8s.com: 192.168.200.22

############################################################################

Kubernetes Worker Nodes:

k8s-wn-01.k8s.com: 192.168.200.30

k8s-wn-02.k8s.com: 192.168.200.31
