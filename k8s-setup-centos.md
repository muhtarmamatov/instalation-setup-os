Install and Configure a Multi-Master HA Kubernetes Cluster with kubeadm, HAProxy and Keepalived on CentOS 7

Pre-requisites
For Kubernetes, we will use 3 control-plane nodes and 3 worker nodes. We will also use 2 servers for HAProxy to provide high availability with a virtual IP addess of 10.11.1.30.

We will need a DNS server. My DNS server configuration is described here and won’t be covered in this article. One small change is that I stopped using hl.local and moved to a reserved top level DNS name hl.test (see rfc2606 for more info).

Below are our requirements for the installation:

2 servers for HAProxy with Keepalived, running CentOS 7, 1 CPU, 1 GB RAM, 8 GB disk.
3 servers for control-plane nodes, running CentOS 7, 2 CPU, 4 GB RAM, 16 GB disk.
3 servers for worker nodes, running CentOS 7, 2 CPU, 6 GB RAM, 16 GB disk.
Full network connectivity between all machines in the cluster.
sudo privileges on all machines.
SSH access from one device to all nodes in the system.
A DNS server with a DNS entry for the HAProxy load balancer pointing to a virtual IP address 10.11.1.30.

Homelab details can be seen in the table below.

Hostname	IP address	RAM (MB)	Notes
admin1.hl.test	10.11.1.2	1024	DNS/DHCP master, NTP, SMTP, HAProxy master, Keepalived
admin2.hl.test	10.11.1.3	1024	DNS/DHCP slave, NTP, SMTP, HAProxy backup, Keepalived
kubelb.hl.test	10.11.1.30	N/A	Virtual IP address for HAProxy/keepalived
srv31.hl.test	10.11.1.31	4096	Kubernetes control plane
srv32.hl.test	10.11.1.32	4096	Kubernetes control plane
srv33.hl.test	10.11.1.33	4096	Kubernetes control plane
srv34.hl.test	10.11.1.34	6144	Kubernetes worker node
srv35.hl.test	10.11.1.35	6144	Kubernetes worker node
srv36.hl.test	10.11.1.36	6144	Kubernetes worker node

These are my PROXMOX hypervisor details.

Hostname	IP address	CPU Cores	RAM (MB)	Notes
kvm1.hl.test	10.11.1.21	8	28672	Physical KVM host with SSD storage
kvm2.hl.test	10.11.1.22	8	16384	Physical KVM host with SSD storage
kvm3.hl.test	10.11.1.23	8	16384	Physical KVM host with SSD storage
