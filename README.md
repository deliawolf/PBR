# Simple PBR Config

Policy-Based Routing (PBR) can be used to implement this kind of routing decision based on source IP address. Here is an example configuration for a Cisco router:

Here's a basic structure of a PBR command:
```
Router(config)# access-list <access-list-number> permit ip <source-network> <wildcard-mask>
Router(config)# route-map <route-map-name> permit <sequence-number>
Router(config-route-map)# match ip address <access-list-number>
Router(config-route-map)# set ip next-hop <next-hop-address>
```

First, create access lists to match the source IP addresses:
```
Router(config)# access-list 10 permit 192.168.1.0 0.0.0.255
Router(config)# access-list 20 permit 192.168.2.0 0.0.0.255
```
Next, create route maps that match the access lists and set the next hop to the appropriate ISP:
```
Router(config)# route-map PBR1 permit 10
Router(config-route-map)# match ip address 10
Router(config-route-map)# set ip next-hop <ISP1's gateway IP>
Router(config)# route-map PBR2 permit 10
Router(config-route-map)# match ip address 20
Router(config-route-map)# set ip next-hop <ISP2's gateway IP>
```
Finally, apply the route maps to the incoming interfaces:
```
Router(config)# interface FastEthernet0/0
Router(config-if)# ip policy route-map PBR1
Router(config)# interface FastEthernet0/1
Router(config-if)# ip policy route-map PBR2
```
Remember to replace <ISP1's gateway IP> and <ISP2's gateway IP> with the actual IP addresses of your ISPs' gateways.
This configuration would direct traffic from the 192.168.1.0/24 network to ISP1 and traffic from the 192.168.2.0/24 network to ISP2. It assumes that the interfaces FastEthernet0/0 and FastEthernet0/1 are connected to the 192.168.1.0/24 and 192.168.2.0/24 networks respectively.

## PBR Example

Example 1:

Suppose you want to redirect all traffic from network 192.168.1.0/24 to a specific next-hop address (10.0.0.1).
```
Router(config)# access-list 10 permit ip 192.168.1.0 0.0.0.255
Router(config)# route-map PBR1 permit 10
Router(config-route-map)# match ip address 10
Router(config-route-map)# set ip next-hop 10.0.0.1
```
Example 2:

You want to redirect HTTP traffic from network 192.168.1.0/24 to a specific next-hop address (10.0.0.1). You will need to use an extended access-list to match on the TCP port number (80 for HTTP).
```
Router(config)# access-list 101 permit tcp 192.168.1.0 0.0.0.255 any eq 80
Router(config)# route-map PBR2 permit 10
Router(config-route-map)# match ip address 101
Router(config-route-map)# set ip next-hop 10.0.0.1
```
Example 3:

You want to redirect all traffic from network 192.168.1.0/24 to a specific next-hop address (10.0.0.1) but have a backup next-hop (10.0.0.2) in case the primary one fails.
```
Router(config)# access-list 10 permit ip 192.168.1.0 0.0.0.255
Router(config)# route-map PBR3 permit 10
Router(config-route-map)# match ip address 10
Router(config-route-map)# set ip next-hop 10.0.0.1 10.0.0.2
```
In this example, if the primary next-hop (10.0.0.1) fails, the router will use the secondary next-hop (10.0.0.2).

Example 4:

You want to redirect traffic from two different source networks to two different next-hop addresses.
```
Router(config)# access-list 10 permit ip 192.168.1.0 0.0.0.255
Router(config)# access-list 20 permit ip 192.168.2.0 0.0.0.255
Router(config)# route-map PBR4 permit 10
Router(config-route-map)# match ip address 10
Router(config-route-map)# set ip next-hop 10.0.0.1
Router(config)# route-map PBR4 permit 20
Router(config-route-map)# match ip address 20
Router(config-route-map)# set ip next-hop 10.0.0.2
```
In this example, traffic from 192.168.1.0/24 goes to 10.0.0.1, and traffic from 192.168.2.0/24 goes to 10.0.0.2. 

Example 5:

You want to redirect all traffic from network 192.168.1.0/24 to a specific next-hop address (10.0.0.1), except for HTTP traffic which should go to a different next-hop address (10.0.0.2).
```
Router(config)# access-list 101 deny tcp 192.168.1.0 0.0.0.255 any eq 80
Router(config)# access-list 101 permit ip 192.168.1.0 0.0.0.255 any
Router(config)# access-list 102 permit tcp 192.168.1.0 0.0.0.255 any eq 80
Router(config)# route-map PBR5 permit 10
Router(config-route-map)# match ip address 101
Router(config-route-map)# set ip next-hop 10.0.0.1
Router(config)# route-map PBR5 permit 20
Router(config-route-map)# match ip address 102
Router(config-route-map)# set ip next-hop 10.0.0.2
```
Example 6:

You want to redirect all UDP traffic from network 192.168.1.0/24 to a specific next-hop address (10.0.0.1).
```
Router(config)# access-list 101 permit udp 192.168.1.0 0.0.0.255 any
Router(config)# route-map PBR6 permit 10
Router(config-route-map)# match ip address 101
Router(config-route-map)# set ip next-hop 10.0.0.1
```
Example 7:

You want to redirect all traffic from a specific host (192.168.1.10) to a specific next-hop address (10.0.0.1).
```
Router(config)# access-list 10 permit host 192.168.1.10
Router(config)# route-map PBR7 permit 10
Router(config-route-map)# match ip address 10
Router(config-route-map)# set ip next-hop 10.0.0.1
```
Example 8:

You want to redirect all traffic except traffic from network 192.168.1.0/24, which should continue to follow the normal routing table.
```
Router(config)# access-list 10 deny 192.168.1.0 0.0.0.255
Router(config)# access-list 10 permit any
Router(config)# route-map PBR8 permit 10
Router(config-route-map)# match ip address 10
Router(config-route-map)# set ip next-hop 10.0.0.1
```
Example 9:

You want to redirect all traffic from network 192.168.1.0/24 to a specific next-hop address (10.0.0.1), unless the destination is 192.168.2.0/24, which should continue to follow the normal routing table.
```
Router(config)# access-list 101 deny 192.168.1.0 0.0.0.255 192.168.2.0 0.0.0.255
Router(config)# access-list 101 permit ip 192.168.1.0 0.0.0.255 any
Router(config)# route-map PBR9 permit 10
Router(config-route-map)# match ip address 101
Router(config-route-map)# set ip next-hop 10.0.0.1
```
Example 10:

You want to redirect all traffic from network 192.168.1.0/24 to a specific next-hop address (10.0.0.1), but only during certain times of the day.
```
Router(config)# access-list 10 permit 192.168.1.0 0.0.0.255
Router(config)# time-range EVERYDAY
Router(config-time-range)# periodic weekdays 8:00 to 17:00
Router(config)# route-map PBR10 permit 10
Router(config-route-map)# match ip address 10
Router(config-route-map)# match time-range EVERYDAY
Router(config-route-map)# set ip next-hop 10.0.0.1
```
Example 11:

You want to redirect HTTP traffic from network 192.168.1.0/24 to a specific next-hop address (10.0.0.1) and HTTPS traffic to another next-hop address (10.0.0.2).
```
Router(config)# access-list 101 permit tcp 192.168.1.0 0.0.0.255 any eq 80
Router(config)# access-list 102 permit tcp 192.168.1.0 0.0.0.255 any eq 443
Router(config)# route-map PBR11 permit 10
Router(config-route-map)# match ip address 101
Router(config-route-map)# set ip next-hop 10.0.0.1
Router(config)# route-map PBR11 permit 20
Router(config-route-map)# match ip address 102
Router(config-route-map)# set ip next-hop 10.0.0.2
```
Example 12:

You want to redirect all traffic from network 192.168.1.0/24 to a specific next-hop address (10.0.0.1) and set the precedence for Quality of Service (QoS) to a certain value (let's say 5).
```
Router(config)# access-list 10 permit 192.168.1.0 0.0.0.255
Router(config)# route-map PBR12 permit 10
Router(config-route-map)# match ip address 10
Router(config-route-map)# set ip next-hop 10.0.0.1
Router(config-route-map)# set ip precedence 5
```
These are just a few examples. PBR can be more complex depending on the requirements. Always test your PBR configuration in a lab environment before deploying them in a production network.
