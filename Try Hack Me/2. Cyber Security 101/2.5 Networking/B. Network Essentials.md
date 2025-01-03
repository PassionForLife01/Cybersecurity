# B. Network Essentials
## DHCP

When we connect to the network, we need configure the following:
- IP address along with the subnet mask
- Router or gateway
- DNS server

DHCP (Dynamic Host Configuration Protocol) will give us a unique IP address in the subnet.
This protocol relies on UDP.
Server listens on UDP port 67, and client sends from UDP port 68.

DHCP follows 4 steps: Discover, Offer, Request, Acknowledge

1. **DHCP Discover**: The client ==broadcasts== a DHCPDISCOVER message seeking the local DHCP server if one exists.
2. **DHCP Offer**: The server responds with a DHCPOFFER message with an IP address available for the client to accept.
3. **DHCP Request**: The client responds with a DHCPREQUEST message to indicate that it has accepted the offered IP.
4. **DHCP Acknowledge**: The server responds with a DHCPACK message to confirm that the offered IP address is now assigned to this client.

![[Pasted image 20250103091007.png]]