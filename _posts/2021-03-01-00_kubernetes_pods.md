---
layout: post
title:  "Understanding kubernetes networking: Pods"
---
One of the most difficult part to understand Kubernetes is how Kubernetes components and layer consists of and how they communicate(network) each other.

I've found wonderful post regarding this by Mark Betz, therefore this post will summarize his posts.

## Pods

A Pod <span style="color: red;"> consists of one or more containers under the same host, and they are configured to share the network and other resources such as Volume.<span><br>
Then what is the meaning of "share a network stack"? Containers in the same Pod can reach each other on the localhost. If container running nginx and listening on port 80 and another container running scrapyd, the second container can connect to the first as http://localhost:80. How this can be done?<br>
<br>

###### Step 1 - Pod with 1 container
<img src="/resources/networking_inside_pod1.png" width="600" height="500"><br>
- We have the physical network interface `eth0`, and `docker0` plays a role as bridge between physical and virtual network, `veth0`.
- We can see that `docker0` and `veth0` are on the same network
- 172.17.0.0/24 is the network and `docker` assgined `172.17.0.1` which mean default gateway for `veth0` assigned with `172.17.0.2`
- Due to the way of configuration of network namespaces when the container is launched, processes inside the container see only `veth0`, and communicate with outside through `docker0` and `eth0`.

###### Step 2-1 - Pod with 2 containers, case 1
<img src="/resources/networking_inside_pod2.png" width="600" height="500"><br>
- Second container is launched, and got a new virtual network interface `veth1` connected to the same `docker0` bridge, this assigned to the network `172.17.0.3`.
- Two containers communicates over the bridge network, that is `docker0`

###### Step 2-2 - Pod with 2 containers, case 2
<img src="/resources/networking_inside_pod3.png" width="600" height="500"><br>
- Alternatively, due to the docker's flexibility, container can be launched under the same virtual network with previous container, which means `veth0`
- In this case, each container's port is opened so that they can reach each other.
- However, this also means that two containers cannot use same port at the same time.

###### Step 3 - Pod with 2 containers and Pause container
<img src="/resources/networking_inside_pod4.png" width="600" height="500"><br>
- Then kubernetes creates special container for each pod which only performs network interface for each other container. The container's command starts with `pause`, you can find this container if you run `docker ps` inside of the kubernetes, suspends current process until a signal is recieved so these containers do nothing at all excepts sleep until kubernetes sends them a SIGTERM(kill)

###### Step 4 - The Pod Network - Cluster with two nodes.
Until the Step3, we now understand how network works inside of Pod, and communicates between pods.
Now lets find out how Nodes can network each other.<br>
<img src="/resources/networking_inside_pod5.png" width="600" height="500"><br>
- The cluster is created under the private network `10.100.0.0/24`, so the router address is `10.100.0.1`.
- Two instaces have address `10.100.0.2` and `10.100.0.3` respectively.
- And if we include the Pod networks describe above, the network diagram can be drawn as below.
<img src="/resources/networking_inside_pod6.png" width="600" height="500"><br>
- The host on the left has interface `eth0` with an address of `10.100.0.2`, whose default gateway is `10.100.0.1`
- Here, the problem comes out, One node typically has no idea what private address space was assigned to a bridge on another node. This will be the problem in case if we try to send packets from the pod in one node to the pod in another node.
- Kubernetes provide two steps to solve this problem.
- First, it assigns the bridges addresses on each nodes within its space, based on the node the bridge is built on.
- Secondly, it adds routing rules to the gateway at `10.100.0.1` telling packing how its routes should be.

Above is the how Pods networks each other, and this networking methods is being call **Overlay Network**.
The final drawing comes like this.
<img src="/resources/networking_inside_pod7.png" width="600" height="500"><br>


Reference:https://medium.com/google-cloud/understanding-kubernetes-networking-pods-7117dd28727