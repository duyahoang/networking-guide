# Comprehensive Summary of BGP

## How Does BGP Work?
- BGP uses TCP as the transport protocol, on port 179. -> reliable connection
  - Two BGP routers form a TCP connection between one another -> termed "peers" or "neighbors".
    - neighbor command: `neighbor <ip-address> remote-as <number>`
    - The two IP addresses are used in the neighbor command of the peer routers must be able to reach one another.
    - Verify reachability by using an extended ping between the two IP addresses. The extended ping forces the pinging router to use the IP address that the neighbor command specifies as the source IP rather than the IP address of the interface from which the packet goes out.
  - The peer routers exchange messages to open and confirm the connection parameters.
    - The values that the routers exchange include the `AS number`, the `BGP version` that the routers run, the `BGP router ID`, and the `keepalive hold time`.
    - After the confirmation and acceptance of these values, `Established` state of the neighbor connection occurs.
    - Any state other than Established -> two routers did not become neighbors -> cannot exchange BGP updates.
    - If there are any BGP configuration changes -> must reset the neighbor connection to allow the new parameters to take effect.
- BGP routers exchange network reachability information.
  - This information is mainly an indication of the full paths that a route must take in order to reach the destination network.
  - The paths are BGP AS numbers.
  - This information helps in the construction of a loop-free AS graph.
  - The graph also shows where to apply routing policies in order to enforce some restrictions on the routing behavior.
- Peering exchange:
  - Initial exchange: Full routing tables.
  - Subsequent: Incremental updates as tables change.
  - BGP keeps a version number of the BGP table. The version number is the same for all the BGP peers -> ensures peers sync.
    - The version number changes whenever BGP updates the table with routing information changes.
    - A version that continues to increment indicates that there is some route flap that causes the continuous update of routes.
  - Keepalive packets -> ensures that the connection between the BGP peers is alive. Notification packets -> address errors or special conditions.
 
## eBGP and iBGP
- If an AS has multiple BGP speakers, the AS can serve as a transit service for other ASs.
- In order to send the information to external ASs, there must be an assurance of the reachability for networks. In order to assure network reachability, these processes take place:
  - Internal BGP (iBGP) peering between routers inside an AS.
  - Redistribution of BGP information to IGPs that run in the AS.
- The `remote AS number` points to either an external or an internal AS, which indicates either eBGP or iBGP.
  - If the remote AS number is the same as the router AS number -> iBGP, otherwise eBGP.
- eBGP peers must have a direct connection or need to use `eBGP Multihop` if a direct connection is unavailable (Multihop only applies to eBGP).
  - By default, eBGP peering has a TTL value of 1 (directly connected). eBGP Multihop increases the TTL value of the IP packet for eBGP sessions.
  - A remote attacker can adjust the TTL of sent packets so that they appear to originate from a directly connected peer.
    - RFC 5082 - The `Generalized TTL Security Mechanism (GTSM)` provides a solution that, instead of accepting only packets with a TTL set to 1, only accepts packets with a TTL of 255 to ensure the originator really is exactly one hop away. This is accomplished on IOS with the TTL security feature. Only BGP messages with an IP TTL greater than or equal to 255 minus the specified hop count will be accepted. TTL security and EBGP multihop are mutually exclusive; eBGP-multihop is no longer needed when TTL security is in use.
  - eBGP learned prefixes are assigned an AD value of 20 upon installing the routes in RIB.
- iBGP peers do not need to have a direct connection, but there must be some IGP that runs and allows the two neighbors to reach one another.
  - The default TTL is 255 because we do not know how many hop counts it will take from one router to reach the next.
  - iBGP learned prefixes are assigned an administrative distance (AD) value of 200 upon installing the routes in RIB.

## BGP and Loopback Interfaces
- The use of a loopback interface to define neighbors is common with iBGP but is not common with eBGP.
- Normally, the loopback interface is used to make sure that the IP address of the neighbor stays up and is independent of hardware that functions properly.
- In the case of eBGP, peer routers frequently have direct connection, and loopback does not apply.
- If the IP address of a loopback interface is in the neighbor command, extra configuration on the neighbor router is needed.
  - The neighbor router needs to inform BGP of the use of a loopback interface as the source in the TCP neighbor connection rather than a physical interface to initiate the BGP neighbor TCP connection.
  - `update-source interface-type interface-number`. For example: `neighbor 10.195.225.11 update-source loopback 1`.
 
## BGP Adjacency States
- BGP uses the finite-state machine (FSM) to maintain a table of all BGP peers and their operational status.
- The BGP session may report the following states:
  - `Idle` State: This initial phase kicks off when a new BGP neighbor is configured or an existing BGP peering is reset. During this state, BGP prepares for connection by initializing resources, setting a `ConnectRetry` timer, and attempting to establish a TCP connection with the remote BGP neighbor. It also listens for incoming connections. If the connection attempt is successful, it progresses to the `Connect` state. If not, it remains `Idle`.
  - `Connect` State: Here, BGP waits for the completion of the TCP three-way handshake. A successful handshake moves the process to the `OpenSent` state. A failure leads to the `Active` state. If the `ConnectRetry` timer expires without a successful connection, the state remains unchanged, and BGP retries the TCP handshake. Any other event, such as a BGP reset, returns the process to the `Idle` state. During this stage, the neighbor with the higher IP address manages the connection. The router initiating the request uses a dynamic source port, but the destination port is always 179
  - `Active` State: In this phase, BGP attempts another TCP three-way handshake with the remote neighbor. Success moves the process to `OpenSent`. If the `ConnectRetry` timer expires, it reverts to the `Connect` state. BGP continues to listen for incoming connections during this phase. Certain events, like resetting BGP, can push the router back to the `Idle` state.
  - `OpenSent` State: BGP waits for an `Open` message from the remote neighbor, which it checks for errors (such as incorrect version numbers, AS numbers, neighbor's IP address, BGP identifiers or router IDs must be unique, password, TTL). Any issues result in a `Notification` message being sent and a return to the `Idle` state. If the `Open` message is valid, BGP sends `Keepalive` messages, resets the keepalive timer, and negotiates the hold time. TCP session failures push BGP back to the `Active` state, while other errors or the expiration of the hold timer result in a `Notification` message and a return to the `Idle` state. Resetting the BGP process also leads back to `Idle`.
  - `OpenConfirm` State: BGP awaits a `Keepalive` message from the remote neighbor. Receiving this message leads to the `Established` state, completing the neighbor adjacency. The hold timer is reset upon receiving a `Keepalive` message. If a `Notification` message is received instead, BGP falls back to the `Idle` state. `Keepalive` messages continue to be sent during this phase.
  - `Established` State: The neighbor relationship is fully established, allowing the BGP routers to exchange routing information through `Update` messages. Receipt of `Keepalive` or `Update` messages resets the hold timer. Receiving a `Notification` message, however, causes a revert to the `Idle` state.
- Each phase ensures a secure and reliable establishment of BGP neighbor adjacencies, facilitating the exchange of routing information between routers.

## BGP Messages
- Messages Function Overview:
  - **Open** Message: Sets up and establishes BGP adjacency.
  - **Keepalive** Message: Ensures that BGP neighbors are still alive.
  - **Update** Message: Advertises, updates, or withdraws routes.
  - **Notification** Message: Indicates an error condition to a BGP neighbor.

- Open Message:
  - The OPEN message is used to establish a BGP adjacency. Both sides negotiate session capabilities before a BGP peering establishes. The OPEN message contains the BGP version number, ASN of the originating router, Hold Time, BGP Identifier, and other optional parameters that establish the session capabilities.
    - Hold Time
      - The Hold Time attribute sets the Hold Timer in seconds for each BGP neighbor. Upon receipt of an UPDATE or KEEPALIVE, the Hold Timer resets to the initial value. If the Hold Timer reaches zero, the BGP session is torn down, routes from that neighbor are removed, and an appropriate update route withdraw message is sent to other BGP neighbors for the impacted prefixes. The Hold Time is a heartbeat mechanism for BGP neighbors to ensure that the neighbor is healthy and alive.
      - When establishing a BGP session, the routers use the smaller Hold Time value contained in the two router’s OPEN messages. The Hold Time value must be at least three seconds, or zero.
      - Best practice is to have a match hold timer, but technically, it is not required to establish neighbor adjacency.
    - BGP Identifier
      - The BGP Router-ID (RID) is a 32-bit unique number that identifies the BGP router in the advertised prefixes as the BGP Identifier. The RID can be used as a loop prevention mechanism for routers advertised within an autonomous system. The RID can be set manually or dynamically for BGP. A nonzero value must be set for routers to become neighbors. The dynamic RID allocation logic varies between the following operating systems.
      - Setting a static BGP RID is a best practice.
- Keepalive Message:
  - BGP does not rely on the TCP connection state to ensure that the neighbors are still alive. Keepalive messages are exchanged every one-third of the Hold Timer agreed upon between the two BGP routers. If the Hold Time is set to zero, no Keepalive messages are sent between the BGP neighbors.
- Update Message:
  - The Update message advertises any feasible routes, withdraws previously advertised routes, or can do both. The Update message includes the `Network Layer Reachability Information (NLRI)` that includes the prefix and associated BGP `Path Attributes` (PAs) when advertising prefixes. Withdrawn NLRIs include only the prefix. An UPDATE message can act as a Keepalive to reduce unnecessary traffic.
- Notification Message:
  - A Notification message is sent when an error is detected with the BGP session, such as a hold timer expiring, neighbor capabilities change, or a BGP session reset is requested. This causes the BGP connection to close.

## Network Command
- The format of the `network` command is:
  - `network <network-number> mask <network-mask>`
- The `network` command controls the networks that originate from this box.
- This concept is different than the familiar configuration with Interior Gateway Routing Protocol (IGRP) and RIP.
- The `network` command does not try to run BGP on a certain interface. Instead, it tries to indicate to BGP what networks BGP must originate from this box. The command uses a mask portion because BGP version 4 (BGP4) can handle subnetting and supernetting.
- The `network` command works if the router knows the network that you attempt to advertise, whether connected, static, or learned dynamically via any other routing prtotocols.

## Loop Prevention
- BGP is a path vector routing protocol and does not contain a complete topology of the network like link state routing protocols. BGP behaves similarly to distance vector protocols to ensure a path is loop-free.
- The BGP attribute `AS_PATH` is a well-known mandatory attribute and includes a complete listing of all the ASNs that the prefix advertisement has traversed from its source AS. The AS_PATH is used as a loop prevention mechanism in the BGP protocol. If a BGP router receives a prefix advertisement with its AS listed in the AS_PATH, it discards the prefix because the router thinks the advertisement forms a loop.

## Route Maps
- Route maps are heavily used with BGP. In the BGP context, the route map is a method to control and modify routing information.
  - The control and modification of routing information occurs through the definition of conditions for route redistribution from one routing protocol to another.
  - Or the control of routing information can occur at injection in and out of BGP.