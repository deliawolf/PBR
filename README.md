# Simple PBR Config

Policy-Based Routing (PBR) can be used to implement this kind of routing decision based on source IP address. Here is an example configuration for a Cisco router:

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
