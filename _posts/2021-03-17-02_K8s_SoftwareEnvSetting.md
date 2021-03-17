---
layout: post
title:  "Kafka Cluster mission"
---

###### Make VM Ready for K8S
Do below process in VM master.<br>
1.<br>
```aidl
setenforce 0
```
2.<br>
```aidl
sed -i 's/^SELINUX=enforcing$/SELINUX=permissive/' /etc/selinux/config
```
3.<br>
```
sestatus
```
4.<br>
```aidl
systemctl stop firewalld && systemctl disable firewalld
```
5.<br>
```aidl
systemctl stop NetworkManager && systemctl disable NetworkManager
```
6.<br>
```aidl
swapoff -a && sed -i '/ swap / s/^/#/' /etc/fstab
```
7.<br>
```aidl
cat <<EOF >  /etc/sysctl.d/k8s.conf
net.bridge.bridge-nf-call-ip6tables = 1
net.bridge.bridge-nf-call-iptables = 1
EOF
sysctl --system
```
8.<br>
```aidl
cat <<EOF > /etc/yum.repos.d/kubernetes.repo
[kubernetes]
name=Kubernetes
baseurl=https://packages.cloud.google.com/yum/repos/kubernetes-el7-x86_64
enabled=1
gpgcheck=1
repo_gpgcheck=1
gpgkey=https://packages.cloud.google.com/yum/doc/yum-key.gpg https://packages.cloud.google.com/yum/doc/rpm-package-key.gpg
exclude=kubelet kubeadm kubectl
EOF
```
9.<br>
```aidl
yum -y update
```
10.<br>
```aidl
cat <<EOF >> /etc/hosts
30.0.2.30 master
30.0.2.31 node1
30.0.2.32 node2
30.0.2.33 node3
30.0.2.34 node4
EOF
```
11.<br>
```aidl
yum install -y yum-utils device-mapper-persistent-data lvm2 
```
12.<br>
```aidl
yum-config-manager --add-repo https://download.docker.com/linux/centos/docker-ce.repo
```
13.<br>
```aidl
yum update -y && yum install -y containerd.io-1.2.13 docker-ce-19.03.11 docker-ce-cli-19.03.11
```
14.<br>
```aidl
mkdir /etc/docker

cat > /etc/docker/daemon.json <<EOF
{
  "exec-opts": ["native.cgroupdriver=systemd"],
  "log-driver": "json-file",
  "log-opts": {
    "max-size": "100m"
  },
  "storage-driver": "overlay2",
  "storage-opts": [
    "overlay2.override_kernel_check=true"
  ]
}
EOF

mkdir -p /etc/systemd/system/docker.service.d
```
15.<br>
```aidl
yum install -y --disableexcludes=kubernetes kubeadm-1.20.4-0.x86_64 kubectl-1.20.4-0.x86_64 kubelet-1.20.4-0.x86_64
```
