---
layout: post
title:  "Setting basic environment for Kubernetes cluster"
---

### Kubernetes Setting
I am trying to set kubernetes on-premise cluster with two laptops, one for Master and other one for Workder node.
To build up machine learning platform project. See this page for the architecture.
#### A. Requirements
- A compatible linux host.<br><br>
You know already. ;)<br><br>
- 2GB or more RAM per each machine.<br><br>
You can get your memory status with below linux command
```aidl
linuxTerminal:~$ free
              total        used        free      shared  buff/cache   available
Mem:       24458056     4630272    15923776      959904     3904008    18474024
swap:             0           0           0

linuxTerminal:~$
```
<br>

- 2CPUs(or CPU cores) or more.<br><br>
You can get your CPUs with below linux command
```aidl
linuxTerminal:~$ cat /proc/cpuinfo | grep processor | wc -l
16
linuxTerminal:~$
```
- Full network connectivity between all machines (publicly or privately).<br><br>
The connectivity between machines can be checked as below<br><br>
**IN CASE OF SAME ROUTER<br>**
This case, it means your two machines are under the same network environment.
Therefore the connectivity can be checked directly using ping command to each other's private IP address.<br><br>
**a. get each machines private IP addresses.**
```aidl
linuxTerminal:~$ ifconfig
lo: flags=73<UP,LOOPBACK,RUNNING>  mtu 65536
        inet 127.0.0.1  netmask 255.0.0.0
        inet6 ::1  prefixlen 128  scopeid 0x10<host>
        loop  txqueuelen 1000  (Local Loopback)
        RX packets 3881  bytes 824349 (824.3 KB)
        RX errors 0  dropped 0  overruns 0  frame 0
        TX packets 3881  bytes 824349 (824.3 KB)
        TX errors 0  dropped 0 overruns 0  carrier 0  collisions 0

wlo1: flags=4163<UP,BROADCAST,RUNNING,MULTICAST>  mtu 1500
        inet 999.99.999.999  netmask 255.255.254.0  broadcast 888.88.888.888
        inet6 xx99::9999:9xx9:99xx:x99  prefixlen 64  scopeid 0x20<link>
        ether 99:9x:99:99:x9:99  txqueuelen 1000  (Ethernet)
        RX packets 123504  bytes 143969757 (143.9 MB)
        RX errors 0  dropped 0  overruns 0  frame 0
        TX packets 39982  bytes 7857742 (7.8 MB)
        TX errors 0  dropped 0 overruns 0  carrier 0  collisions 0

linuxTerminal:~$
```
You can see 'wl01' inet 999.99.999.999. This is your private IP address. Repeat above command on each machines,
And use ping command at each other's command line.<br>
```aidl
linuxTerminal:~$ ping 999.99.999.999
PING 888.88.888.888 (999.99.999.999) 56(84) bytes of data.
64 bytes from 999.99.999.999: icmp_seq=1 ttl=64 time=0.084 ms
64 bytes from 999.99.999.999: icmp_seq=2 ttl=64 time=0.074 ms
64 bytes from 999.99.999.999: icmp_seq=3 ttl=64 time=0.080 ms
^C
--- 999.99.999.999 ping statistics ---
3 packets transmitted, 5 received, 0% packet loss, time 4102ms
rtt min/avg/max/mdev = 0.074/0.079/0.084/0.003 ms

linuxTerminal:~$
```
888.88.888.888 is private IP addresss of your machine and 999.99.999.999 is private IP address of the target machine.
You should do this process for cartesian cases of machines.
It ping does not working, this means your network is not connected.(Whether this means your network is not under the same router or router network policy does not allow this.(Usually you can see this in the public area such as Starbucks.))
- Unique hostName, MAC address, and product UUID for every each node.<br>
Usually,MAC address and product UUID is different in most cases, so I will skip checking theses two particular, however, hostName can be checked with below command in Linux cmd.<br>
```aidl
linuxTerminal:~$ hostname
yourHostName
linuxTerminal:~$
```
<br>
Additionally, you should let iptables to see bridge traffic. Therefore run below command.
Make sure `net.bridge.bridge-nf-call-iptables=1` in your 'sysctl'.

```aidl
cat <<EOF | sudo tee /etc/modules-load.d/k8s.conf
br_netfilter
EOF

cat <<EOF | sudo tee /etc/sysctl.d/k8s.conf
net.bridge.bridge-nf-call-ip6tables = 1
net.bridge.bridge-nf-call-iptables = 1
EOF
sudo sysctl --system
```
<br>

Also, turn off SELinux(Security) also.<br>
```aidl
linuxTerminal:~$ sudo setenforce 0
setenforce: SELinux is disabled

linuxTerminal:~$
```

- Necessary ports are open on each machine.(see below)
<br><br>

  ######for MASTER NODE
  |Protocol|Direction|Port Range|Purpose|UsedBy|
  |---|:---:|:---:|:---:|---:|
  |TCP|Inbound|6443*|Kubernetes API Server|All|
  |TCP|Inbound|2379-2380|etcd server client API|kube-apiserver, etcd|
  |TCP|Inbound|10250|kubelet API|Self, Control plane|
  |TCP|Inbound|10251|kube-scheduler|Self|
  |TCP|Inbound|10252|kube-controller-manager|Self|
  <br>

  Run below commands to open ports.<br>
  ```aidl
  linuxTerminal:~$ sudo firewall-cmd --add-port 6443/tcp --permanent &
                   sudo firewall-cmd --add-port 2379-2380/tcp --permanent &
                   sudo firewall-cmd --add-port 10250-10252/tcp --permanent &
                   sudo firewall-cmd --reload

  linuxTerminal:~$
  ```
  <br>

  ######for WORKER NODE
  |Protocol|Direction|Port Range|Purpose|UsedBy|
  |---|:---:|:---:|:---:|---:|
  |TCP|Inbound|10250|kubelet API|Self, Control plane|
  |TCP|Inbound|30000-32767|NodePort Services†|All|
  <br>

  Run below commands to open ports.<br>
  ```aidl
  linuxTerminal:~$ sudo firewall-cmd --add-port 10250/tcp --permanent
                   sudo firewall-cmd --add-port 30000-32767/tcp --permanent
                   sudo firewall-cmd --reload
  linuxTerminal:~$
  ```
  <br>

 - Swap disabled. You MUST disable swap in order for the kubelet to work properly.<br><br>
   You can turn off your swap memory. The reason behind this is, Kuberenetes' motto represent "efficient resource manage under given allowance."
   Therefore, they don't want to dynamic range of the memory, too.<br>

     ```aidl
     linuxTerminal:~$ sudo swapoff -a
     linuxTerminal:~$
     ```

<br>

#### B. Install runtime/kubeadm/kubectl/kubelet
##### 1). Install runtime
Basically, kubernetes uses the Container Runtime Interface(CRI) to interface with chosen container runtime.
(It was docker before, but to avoid service dependacy, CNCF move to general CRI to support various container runtime services).
Here, I will use docker as container runtime.
- Install Docker CE<br>
```aidl
sudo apt-get update && sudo apt-get install -y \
  apt-transport-https ca-certificates curl software-properties-common gnupg2
```
<br>

- Add Docker's official GPG key
```aidl
curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo apt-key --keyring /etc/apt/trusted.gpg.d/docker.gpg add -
```

- Add Docker's apt repository
```aidl
sudo add-apt-repository \
  "deb [arch=amd64] https://download.docker.com/linux/ubuntu \
  $(lsb_release -cs) \
  stable"
```

- Install Docker CE<br>
Kubernetes uses version 19 (based on posting date 2021/3/08) not the latest docker version 20
```aidl
sudo apt-get update && sudo apt-get install -y \
  containerd.io=1.2.13-2 \
  docker-ce=5:19.03.11~3-0~ubuntu-$(lsb_release -cs) \
  docker-ce-cli=5:19.03.11~3-0~ubuntu-$(lsb_release -cs)
```

- create '/etc/docker'
```aidl
sudo mkdir /etc/docker
```
- Set up the Docker Demon
```aidl
cat <<EOF | sudo tee /etc/docker/daemon.json
{
  "exec-opts": ["native.cgroupdriver=systemd"],
  "log-driver": "json-file",
  "log-opts": {
    "max-size": "100m"
  },
  "storage-driver": "overlay2"
}
EOF
```

- Create /etc/systemd/docker.service.d
```aidl
sudo mkdir -p /etc/systemd/system/docker.service.d
```

- Restart Docker
```aidl
sudo systemctl daemon-reload
sudo systemctl restart docker
```

- set service on boot
```aidl
sudo systemctl enable docker
```
##### 2). Install kubeadm, kubectl and kubelet
`kubeadm`: the command to bootstrap the cluster.<br>
`kubelet`: the component that runs on all of the machines in your cluster and does things like starting pods and containers.<br>
`kubectl`: the command line util to talk the cluster.<br>

** Important: make sure that kubeadm,kubelet and kubectl in the same version.
In case you used minikube before, make your you deleted minikube completely including kubectl installed by minikube.<br>

```
sudo apt-get update && sudo apt-get install -y apt-transport-https curl
curl -s https://packages.cloud.google.com/apt/doc/apt-key.gpg | sudo apt-key add -
cat <<EOF | sudo tee /etc/apt/sources.list.d/kubernetes.list
deb https://apt.kubernetes.io/ kubernetes-xenial main
EOF
sudo apt-get update
sudo apt-get install -y kubelet kubeadm kubectl
sudo apt-mark hold kubelet kubeadm kubectl
```

Basic Environment is ready..!
Now we need to create kubernetes cluster by using `kubeadm`