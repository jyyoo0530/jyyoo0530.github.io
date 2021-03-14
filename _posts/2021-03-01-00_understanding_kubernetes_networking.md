---
layout: post
title:  "Understanding Kubernetes Networking"
---
## Understanding kubernetes networking

### Pods
<img src="/resources/networking_inside_pod.png" width="350" height="320"><br>
From above picture,
- `eth0` is the physical network interface.
- `docker0` is the bridge between physical network and virtual interface `veth0`
- `docker0` and `veth0` is tied together under the same network, for example, 172.17.0.0/24.<br>
  Therefore, `docker0` works like default gateway for container network `veth0`.