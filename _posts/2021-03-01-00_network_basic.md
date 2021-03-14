---
layout: post
title:  "Understanding IP Addresses, Subnets, and CIDR Notation for Networking"
---

### Understanding IP Address
Every device can be addressable.
IP address allows network resources to be reached through a network interface.
Each IP address is unique on its own network. Network can be isolated, and can be bridged and translated to provide access between distinct networks.
**NAT** allows the address to be re-written when packet traverse network borders to allow them to continue on to their correct destination. This allows same IP address to be used on multiple.

##### Difference between IPv4 and IPv6
IPv4 addresses are 32-bit, such as `192.168.0.0`, IPv6 addresses are 128-bit which can handles huge amount of internet devices exists nowadays.

##### IPv4 Classes and Reserved Ranges
IPv4 is divided into five different "classes".<br>
**Class A**: 0.0.0.0 ~ 127.255.255.255<br>: first octets for network, remaindings for <br>
**Class B**: 128.0.0.0 ~ 191.255.255.255<br>: <br>
**Class C**: 192.0.0.0 ~ 223.255.255.255<br>: <br>
**Class D**: 224.0.0.0 ~ 239.255.255.255<br>: <br>
**Class E**: 240.0.0.0 ~ 255.255.255.255<br>: <br>





Reference: https://www.digitalocean.com/community/tutorials/understanding-ip-addresses-subnets-and-cidr-notation-for-networking