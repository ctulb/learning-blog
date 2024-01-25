---
title: 'BIG-IP NEXT Single-Arm Topology'
description: 'Configuring the infrastructure to support a single-arm instance'
pubDate: 'Jan 25 2024'
heroImage: 'a.png'
---

In the last post, I deployed the OVAs for the Next Central manager and Next instance and registered the instance to the CM. However, with just a management connection, the instance isn't going to be able to do much at the moment! I'll deploy some supporting infrastructure to allow for configuration and testing. I'll configure the following:

![Diagram of intended supporting network topology - three networks; outside, dmz and inside; single arm Next deployment in the dmz, docker host on the inside](https://imgur.com/MgLLse2.png)

Next instances retain the concept of VLANs from the last generation BIG-IP appliances - a VLAN (tagged or untagged) needs to be tied to a network interface on the instance to make it available to pass traffic. I've taken down the instance VM and added the required DMZ network interface - I'll be using a single-arm topology in this lab where outside IP addresses are translated through the perimeter firewall to DMZ addresses on the Next instance. The Next instance will then source the connection to the real internal services from a self IP through the firewall. This will help me in future firewall labbing when testing Next-Gen Firewall/IPS functions.

Once the VM has come back up, I can edit the interface configuration on the instance via Infrastructure > Instances > next-1.test.lab > Edit. Then under Network configure the following:

- Networking > L1 networks
  - L1 network name: `dmz`
  - Interface: `1.1`
- Networking > VLANs
  - Name: `dmz`
  - Tag: `0`
  - L1 network: `dmz`
- Networking > IP addresses
  - Address: `10.16.80.187/24`
  - Device Name: left blank
  - VLAN: `dmz`


