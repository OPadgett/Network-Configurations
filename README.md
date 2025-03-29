# Network-Configurations
# Network Configuration Scripts for IT Infrastructure

This repository contains essential network configurations for various networking tasks, including NAT, VPN, ACLs, VLANs, OSPF, and switch security settings. These configurations are designed for Cisco devices and provide a strong foundation for IT engineers working in networking environments.

## 1. NAT (Network Address Translation) - Dynamic NAT Setup

NAT is used to translate private IP addresses to a public IP for outbound traffic. This setup uses a dynamic NAT pool to assign public IP addresses to internal users dynamically.

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

## 2. VPN (Virtual Private Network) - Site-to-Site VPN (Cisco ASA)

VPNs establish a secure connection between remote networks. This configuration sets up a site-to-site VPN using IPsec on a Cisco ASA device.

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

## 3. ACL (Access Control Lists) - Extended ACL Example

ACLs control traffic flow within a network by allowing or denying packets based on predefined rules. This configuration allows HTTP and ICMP traffic but blocks all other traffic.

```
access-list 101 permit tcp 192.168.1.0 0.0.0.255 any eq 80
access-list 101 permit icmp any any
access-list 101 deny ip any any log
interface GigabitEthernet0/0
ip access-group 101 in
```

## 4. VLAN (Virtual Local Area Network) - VLAN and Trunking

VLANs segment network traffic to improve security and efficiency. Trunking is used to allow multiple VLANs to communicate over a single link.

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

## 5. OSPF (Open Shortest Path First) - Routing Protocol Setup

OSPF is a dynamic routing protocol used to exchange routing information within an autonomous system. This configuration sets up OSPF on two network segments.

```
router ospf 1
network 192.168.1.0 0.0.0.255 area 0
network 10.1.1.0 0.0.0.255 area 0
exit
write memory
```

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

These configurations are commonly used in enterprise networks to enhance security, optimize traffic, and ensure reliable connectivity. Feel free to modify them based on your network requirements!
