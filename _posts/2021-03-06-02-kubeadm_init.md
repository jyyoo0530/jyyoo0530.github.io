---
layout: post
title:  "Kubeadm init"
---
### Initiating Kubernetes Cluster with Kubeadm bootstrapper
From previous chapter, you might have 1 master node and 1 workder node.<br>
This chapter will describe how to initiate kubernetes cluster by using kubeadm.<br>
#### Initiating Basic

Basic command is `sudo kubeadm init`, then required additional arguements with starting `--argument=value`.<br>
Below is basic arguments required to understand
- `--apiserver-advertise-address=xxx.xxx.xxx.xxx`<br>
When Node joins into the master, Node will join with above pre-declared apiserver to join into the cluster. Ip address should be private ip address of the master node. (In case your node joins from outer network, you should declare endpoint with public ip address)<br>
- `--pod-network-cidr=xxx.xxx.xxx.xxx`
To let pods communicate each other, it requires kubernetes plugins to be installed. Most common practice is using "Calico" plugins.(however you can use such as flannel). Basic pod network address for Calico is `192.168.0.0/16`
- `--ignore-preflight-errors=all`
Sometimes, due to security check, initiating phases can be interrupted with time out. Even you passthrough the pre-flights, it still shows any warning from preflight check, therefore you can skip it.
<br>
<br>
Aboves are the basic mandatory argument to initiate kubernetes cluster.<br>
Run below commands will initiate kubernetes cluster.
```aidl
sudo kubeadm init --apiserver-advertise-address=xxx.xxx.xxx.xxx --pod-network.cidr=192.168.0.0/16 --ignore-preflight-errors=all
```