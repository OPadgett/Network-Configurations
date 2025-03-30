# Network-Configurations
# Network Configuration Scripts for IT Infrastructure (Bash)

This repository contains essential network configurations for various networking tasks, including NAT, VPN, ACLs, VLANs, OSPF, and switch security settings. These configurations are designed for Cisco devices and provide a strong foundation for IT engineers working in networking environments.

These configurations are commonly used in enterprise networks to enhance security, optimize traffic, and ensure reliable connectivity. Feel free to modify them based on your network requirements!

## 1. NAT (Network Address Translation) - Dynamic NAT Setup

NAT is used to translate private IP addresses to a public IP for outbound traffic. This setup uses a dynamic NAT pool to assign public IP addresses to internal users dynamically.
This code is a configuration for Network Address Translation (NAT) on a Cisco router. It configures NAT for the inside network (private IP addresses) and translates them to a pool of public IP addresses for outbound traffic. 
```
enable
configure terminal
ip nat pool MYPOOL 203.0.113.1 203.0.113.10 netmask 255.255.255.0
access-list 1 permit 192.168.1.0 0.0.0.255
ip nat inside source list 1 pool MYPOOL overload
interface GigabitEthernet0/0
ip nat inside
exit
interface GigabitEthernet0/1
ip nat outside
exit
write memory
```
## Summary ##
A NAT pool (MYPOOL) is configured with public IP addresses 203.0.113.1 to 203.0.113.10.

ACL 1 is created to permit the 192.168.1.0/24 private network for NAT.

The NAT configuration translates traffic from the inside network (private IP addresses) to the NAT pool (public IP addresses) using Port Address Translation (PAT).

GigabitEthernet0/0 is configured as the inside interface, and GigabitEthernet0/1 is configured as the outside interface.

The configuration is saved to the router’s memory.
## 2. VPN (Virtual Private Network) - Site-to-Site VPN (Cisco ASA)

VPNs establish a secure connection between remote networks. This configuration sets up a site-to-site VPN using IPsec on a Cisco ASA device.
This configuration is for setting up IPsec (Internet Protocol Security) VPN on a Cisco router, which includes configuring ISAKMP (Internet Security Association and Key Management Protocol) for secure communication, defining IPsec transform sets, and applying a crypto map to a specific interface.
```
crypto isakmp policy 10
encryption aes
hash sha
authentication pre-share
group 2
lifetime 86400
exit
crypto isakmp key MySecretKey address 203.0.113.2
crypto ipsec transform-set MYSET esp-aes esp-sha-hmac
crypto map MYMAP 10 ipsec-isakmp
set peer 203.0.113.2
set transform-set MYSET
match address 101
interface GigabitEthernet0/0
crypto map MYMAP
exit
```
## Summary ##
ISAKMP Policy: Defines the security parameters for establishing a secure tunnel using ISAKMP (encryption, hashing, authentication, etc.).

Pre-shared Key: Configures a shared secret (MySecretKey) for authentication with the peer at 203.0.113.2.

IPsec Transform Set: Defines the encryption (AES) and integrity (SHA) algorithms for the IPsec tunnel.

Crypto Map: Configures the settings for the IPsec VPN tunnel, including the peer's IP address, the transform set to use, and the ACL that matches the traffic to be encrypted.

Interface Configuration: Applies the crypto map to the GigabitEthernet0/0 interface to encrypt traffic matching the ACL.
## 3. ACL (Access Control Lists) - Extended ACL Example

ACLs control traffic flow within a network by allowing or denying packets based on predefined rules. This configuration allows HTTP and ICMP traffic but blocks all other traffic.
This configuration defines an Access Control List (ACL) 101 to control inbound traffic on interface GigabitEthernet0/0. It allows specific types of traffic while blocking and logging all other traffic.
```
access-list 101 permit tcp 192.168.1.0 0.0.0.255 any eq 80
access-list 101 permit icmp any any
access-list 101 deny ip any any log
interface GigabitEthernet0/0
ip access-group 101 in
```
## Summary ##
Allowed:

✅ HTTP (port 80) traffic from 192.168.1.0/24 to any destination.

✅ ICMP (ping) traffic between any source and destination.

Blocked & Logged:

❌ All other incoming traffic on GigabitEthernet0/0 is denied and logged.
This setup is commonly used on a router's LAN interface to allow web browsing and network diagnostics while blocking unwanted access attempts.
## 4. VLAN (Virtual Local Area Network) - VLAN and Trunking

VLANs segment network traffic to improve security and efficiency. Trunking is used to allow multiple VLANs to communicate over a single link.
This code is a series of Cisco switch commands used to configure VLANs (Virtual LANs), assign VLANs to specific switch ports, and set trunking for inter-VLAN communication.
```
vlan 10
name HR_Department
vlan 20
name IT_Department
exit
interface GigabitEthernet0/1
switchport mode access
switchport access vlan 10
exit
interface GigabitEthernet0/2
switchport mode access
switchport access vlan 20
exit
interface GigabitEthernet0/3
switchport mode trunk
switchport trunk allowed vlan 10,20
exit
write memory
```
## Summary ##
VLAN 10 (HR_Department) is assigned to port GigabitEthernet0/1.

VLAN 20 (IT_Department) is assigned to port GigabitEthernet0/2.

GigabitEthernet0/3 is configured as a trunk port, allowing traffic for both VLAN 10 and VLAN 20.

Finally, the configuration is saved to memory to persist across reboots.
## 5. OSPF (Open Shortest Path First) - Routing Protocol Setup

OSPF is a dynamic routing protocol used to exchange routing information within an autonomous system. This configuration sets up OSPF on two network segments.
This configuration sets up OSPF (Open Shortest Path First) routing on a Cisco router, enabling dynamic routing for specified networks in OSPF Area 0 (the backbone area).
```
router ospf 1
network 192.168.1.0 0.0.0.255 area 0
network 10.1.1.0 0.0.0.255 area 0
exit
write memory
```
## Summary ##
Enabled OSPF process 1 on the router.

Advertised two networks (192.168.1.0/24 and 10.1.1.0/24) in OSPF Area 0.

Ensured the configuration is saved permanently.

This setup allows the router to dynamically exchange routing information with other OSPF-enabled routers, enabling efficient route selection and redundancy.
## 6. Configuring Switches - Port Security

Port security restricts the number of MAC addresses allowed on a switch port, enhancing security by preventing unauthorized devices from connecting.

```
interface GigabitEthernet0/1
switchport mode access
switchport access vlan 10
switchport port-security
switchport port-security maximum 2
switchport port-security violation restrict
switchport port-security mac-address sticky
exit
write memory
```
## Summary ##
Configured GigabitEthernet0/1 as an access port for VLAN 10.

Enabled port security to:

Allow a maximum of 2 devices.

Restrict additional MAC addresses (blocks and logs violations).

Learn MAC addresses dynamically (sticky MAC).

Saved the configuration to NVRAM.

This setup enhances security by preventing unauthorized devices from connecting while ensuring legitimate devices remain operational.
