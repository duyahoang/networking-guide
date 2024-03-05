# Multiprotocol Label Switching (MPLS)

## Overview:
- Multiprotocol Label Switching (MPLS) is a adaptable solution to address the problems faced by present day networks speed, scalability, quality of service (QoS) management, and traffic engineering.
- MPLS allows most packets to be forwarded at the Layer 2 (switching) level rather than at the Layer 3 (routing) level.
- The key to its operation is the technique of labeling packets.

## What is MPLS?
- MPLS is a protocol for speeding up and managing traffic flows across enterprise and service provider networks.
- It operates between traditional Layer 2 (Data Link Layer) networking (such as Ethernet or ATM) and Layer 3 (Network Layer) protocols like IP.
- By using labels, MPLS prioritizes packets, enabling more efficient data flow across the network.

## How MPLS Works:
- **Labeling Packets:** In MPLS, packets are assigned labels at the ingress (entry) point of the MPLS network. Each label is a short, fixed-length, locally significant identifier which is used to make forwarding decisions. The label essentially tells the network how to handle the packet without the need to inspect the packet itself deeply.
- **MPLS Routers:** There are two types of routers in an MPLS network: `Label Edge Routers (LERs)` and `Label Switch Routers (LSRs)`. 
  - LERs are situated at the edge of the MPLS network and assign labels to incoming packets.
  - LSRs are in the core of the MPLS network and use the labels to route packets to their destinations efficiently.
- `Label Switched Paths (LSPs)`: LSPs are established paths that labeled packets follow through the MPLS network. These paths are set up in advance and allow packets to be forwarded at high speed. Traffic engineering is used to optimize the flow of traffic on these paths, taking into account the network's current load, and the importance and requirements of the data being sent.
- **Stacking Labels:** MPLS allows for stacking labels, meaning that a packet can carry multiple labels, organized in a last-in, first-out (LIFO) stack. This feature is useful for nested MPLS applications, like VPNs over traffic-engineered paths.

## Advantages of MPLS:
- Speed: By allowing packets to be forwarded at the Layer 2 level, MPLS significantly increases the speed of data transmission within the network.
- Scalability: MPLS can handle a vast number of paths, making it highly scalable and suitable for expanding networks.
- Quality of Service (QoS): MPLS can ensure a high level of QoS by prioritizing packets. This is crucial for latency-sensitive applications like VoIP and multimedia streaming.
- Traffic Engineering: MPLS gives network operators the ability to direct packets over a predetermined path, avoiding congestion and optimizing the use of the network's resources.
- Flexibility: MPLS supports a wide range of access technologies and connectivity options, making it versatile for various network configurations.

## Applications of MPLS:
- VPN Services: MPLS is widely used in creating VPNs, offering customers secure, reliable connectivity over a shared network infrastructure.
- Improved Bandwidth Utilization: By directing packets through less congested paths, MPLS can improve the overall utilization of network bandwidth.
- Disaster Recovery: MPLS's flexibility in routing can be leveraged for disaster recovery purposes, rerouting traffic around failure points in the network.
