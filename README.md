# Advanced Cybersecurity
 
# About This Repository

Welcome to the **Advanced Cybersecurity Scenarios** repository—an in-depth resource crafted for networking professionals and cybersecurity enthusiasts looking to elevate their skills beyond foundational concepts. This repository delves into advanced lab scenarios, intricate configurations, and real-world troubleshooting exercises aimed at mastering network defense strategies.

## 🔐 Key Focus Areas
- **Advanced VPN Configurations** (IPSec, SSL, DMVPN)  
- **Next-Generation Firewall Setups** and advanced rule management  
- **Intrusion Detection and Prevention Systems (IDS/IPS)** configuration  
- **Advanced Access Control** using AAA
- **Security Hardening** for routers, switches, and endpoint evices  
- **Incident Response Scenarios** and log analysis  
- **Zero Trust Architecture** fundamentals  

> 💡 *Labs are designed for compatibility with **Cisco Packet Tracer, GNS3, and EVE-NG**, complete with detailed instructions for setup, execution, and best practices.*

---

## ⚠️ Important Notice
This repository may include IP addresses, usernames, and passwords for simulation purposes. Always handle this information responsibly, ensuring its use is strictly for educational and ethical training purposes.




## Scenario 1 Summary

This scenario provides a hands-on approach to configuring and securing network traffic using Cisco's Zone-Based Policy Firewall (ZPF). 

### **Network Topology Overview**


### **Part 1: Basic Router Configuration**
1. **Configure Host Names, Interface IP Addresses, and Access Passwords**

```bash
# R1 Basic Configuration
R1(config)# hostname R1
R1(config)# interface g0/1
R1(config-if)# ip address 192.168.1.1 255.255.255.0
R1(config-if)# no shutdown
R1(config)# interface s0/0/0
R1(config-if)# ip address 10.1.1.1 255.255.255.252
R1(config-if)# no shutdown
R1(config)# enable secret cisco12345
```

> Repeat similar steps for R2 and R3.

2. **Configure Static Routes**

```bash
# R1 Static Route to R3 Network
R1(config)# ip route 192.168.3.0 255.255.255.0 10.1.1.2
R1(config)# ip route 192.168.33.0 255.255.255.0 10.1.1.2

# R2 Static Routes
R2(config)# ip route 192.168.1.0 255.255.255.0 10.1.1.1
R2(config)# ip route 192.168.3.0 255.255.255.0 10.2.2.1
R2(config)# ip route 192.168.33.0 255.255.255.0 10.2.2.1

# R3 Static Routes
R3(config)# ip route 192.168.1.0 255.255.255.0 10.2.2.2
```

---

### **Part 2: Configuring a Zone-Based Policy Firewall (ZPF)**

Zone-Based Policy Firewalls (ZPF) are a modern approach to firewall configuration on Cisco devices. Unlike traditional ACL-based filtering, ZPF provides a more flexible and scalable method by grouping interfaces into security zones and defining policies that regulate traffic between these zones. This approach enhances security posture, simplifies management, and aligns with the principle of least privilege.

1. **Define Security Zones**
```bash
R1(config)# zone security INSIDE
R1(config)# zone security OUTSIDE
```

2. **Assign Interfaces to Zones**
```bash
R1(config)# interface g0/1
R1(config-if)# zone-member security INSIDE
R1(config)# interface s0/0/0
R1(config-if)# zone-member security OUTSIDE
```

3. **Configure Class Maps (Define Traffic Types)**
```bash
R1(config)# class-map type inspect match-any CM_HTTP
R1(config-cmap)# match protocol http
```

4. **Configure Policy Maps (Define Actions)**
```bash
R1(config)# policy-map type inspect PM_INSIDE_TO_OUTSIDE
R1(config-pmap)# class type inspect CM_HTTP
R1(config-pmap-c)# inspect
```

5. **Apply Policy to Zone Pair**
```bash
R1(config)# zone-pair security ZP_INSIDE_TO_OUTSIDE source INSIDE destination OUTSIDE
R1(config-sec-zone-pair)# service-policy type inspect PM_INSIDE_TO_OUTSIDE
```

---

### **Verification Commands**
```bash
# Check Interface Zone Assignments
R1# show zone security

# Check Zone Pair and Policies
R1# show zone-pair security
R1# show policy-map type inspect zone-pair

# Check Active Sessions
R1# show policy-map type inspect zone-pair sessions
```



### Why do we use ZPF?
- ZPFs provide a structured and efficient approach to securing network traffic by categorizing interfaces into zones and applying policies that define how traffic flows between them.
- This method ensures granular control over traffic, minimizes unauthorized access, and simplifies the management of complex security rules.
- Using ZPF in this scenario helps protect internal resources, enforce security policies, and maintain secure communication channels across the network.



## Scenario 2 Summary
This scenario focuses on configuring Cisco ASA 5505 with advanced security settings, including interface configurations, routing, NAT, ACLs, and secure remote access to enhance network security and control.

## **Objectives**

### **Part 1: Basic Router, Switch, and PC Configuration**

1. **Configure Hostnames, Interface IP Addresses, and Passwords**

```bash
# Example for R1 Basic Configuration
R1(config)# hostname R1
R1(config)# interface g0/0
R1(config-if)# ip address 209.165.200.225 255.255.255.248
R1(config-if)# no shutdown
R1(config)# enable secret cisco12345
```
> Repeat similar configurations for R2, R3, switches, and PCs with the given IP addressing scheme.

2. **Configure Static Routing, Including Default Routes**
```bash
# R1 Default Route
R1(config)# ip route 0.0.0.0 0.0.0.0 10.1.1.2

# R2 Static Routes
R2(config)# ip route 209.165.200.224 255.255.255.248 10.1.1.1
R2(config)# ip route 172.16.3.0 255.255.255.0 10.2.2.1

# R3 Static Routes
R3(config)# ip route 209.165.200.224 255.255.255.248 10.2.2.2
```

3. **Enable HTTP and SSH Access for R1**
```bash
R1(config)# ip http server
R1(config)# ip http secure-server
R1(config)# username admin privilege 15 secret cisco12345
R1(config)# line vty 0 4
R1(config-line)# login local
R1(config-line)# transport input ssh
```

4. **Configure PC Host IP Settings**
> Configure the NIC settings for each PC using the provided IP addresses.

5. **Verify Connectivity**
```bash
PC-A> ping 192.168.1.1
PC-B> ping 192.168.2.1
```

7. **Save Configurations**
```bash
R1# write memory
```

---

### **Part 2: Accessing the ASA Console and Using CLI Setup Mode**
1. **Access the ASA Console**
```bash
ciscoasa> enable
Password: cisco12345
ciscoasa# show version
```
2. **View Interfaces, Licenses, and Flash Contents**
```bash
ciscoasa# show interface ip brief
ciscoasa# show license
ciscoasa# dir
```
3. **Basic Setup Configuration**
```bash
ciscoasa(config)# hostname ASA5505
ciscoasa(config)# enable password cisco12345
ciscoasa(config)# clock set 15:00:00 March 13 2025
```

---

### **Part 3: Configuring Basic ASA Settings and Interface Security Levels**
1. **Configure Hostname and Domain Name**
```bash
ciscoasa(config)# hostname ASA5505
ciscoasa(config)# domain-name example.com
```
2. **Configure Passwords**
```bash
ciscoasa(config)# username admin password cisco12345 privilege 15
```
3. **Set Date and Time**
```bash
ciscoasa(config)# clock set 15:00:00 March 13 2025
```
4. **Configure Interfaces**
```bash
ciscoasa(config)# interface vlan 1
ciscoasa(config-if)# nameif inside
ciscoasa(config-if)# security-level 100
ciscoasa(config-if)# ip address 192.168.1.1 255.255.255.0
ciscoasa(config-if)# no shutdown

ciscoasa(config)# interface vlan 2
ciscoasa(config-if)# nameif outside
ciscoasa(config-if)# security-level 0
ciscoasa(config-if)# ip address 209.165.200.226 255.255.255.248
ciscoasa(config-if)# no shutdown
```

---

### **Part 4: Configuring Routing, NAT, and Inspection Policy**
1. **Configure Static Default Route**
```bash
ciscoasa(config)# route outside 0.0.0.0 0.0.0.0 209.165.200.225
```
2. **Configure PAT and Network Objects**
```bash
ciscoasa(config)# object network obj_any
ciscoasa(config-network-object)# subnet 0.0.0.0 0.0.0.0
ciscoasa(config-network-object)# nat (inside,outside) dynamic interface
```
3. **Modify Global Service Policy**
```bash
ciscoasa(config)# policy-map global_policy
ciscoasa(config-pmap)# class inspection_default
ciscoasa(config-pmap-c)# inspect ftp
```

---

### **Part 5: Configuring DHCP, AAA, and SSH**
1. **Configure DHCP Server**
```bash
ciscoasa(config)# dhcpd address 192.168.1.10-192.168.1.100 inside
ciscoasa(config)# dhcpd enable inside
```
2. **Configure AAA Authentication**
```bash
ciscoasa(config)# aaa authentication ssh console LOCAL
```
3. **Enable SSH Access**
```bash
ciscoasa(config)# ssh 192.168.1.0 255.255.255.0 inside
ciscoasa(config)# ssh timeout 30
```

---

### **Part 6: Configuring DMZ, Static NAT, and ACLs**
1. **Configure DMZ Interface**
```bash
ciscoasa(config)# interface vlan 3
ciscoasa(config-if)# nameif dmz
ciscoasa(config-if)# security-level 50
ciscoasa(config-if)# ip address 192.168.2.1 255.255.255.0
```
2. **Configure Static NAT for DMZ**
```bash
ciscoasa(config)# object network obj_dmz
ciscoasa(config-network-object)# host 192.168.2.3
ciscoasa(config-network-object)# nat (dmz,outside) static interface service tcp 80 80
```
3. **Configure ACL for DMZ Access**
```bash
ciscoasa(config)# access-list outside_access_in extended permit tcp any host 209.165.200.226 eq 80
ciscoasa(config)# access-group outside_access_in in interface outside
```

---

### Why do we use Firewalls for Security
- This scenario covered the comprehensive configuration of ASA 5505 using CLI, focusing on establishing a secure and scalable network infrastructure.
- Implementing these configurations ensures secure traffic flows, controlled access via NAT and ACLs, and robust firewall policies.
- The structured approach using zones, NAT, ACLs, and inspection policies helps in maintaining security standards and enables efficient network traffic management.
- 



# Scenario 3 Summary

## Introduction
This project demonstrates the configuration of a site-to-site IPsec VPN between two routers (R1 and R2) in a simulated network topology. The VPN tunnel secures traffic flow between the LANs of R1 and R2, with R2 acting as a pass-through. The VPN configuration ensures secure transmission over unprotected networks, leveraging IPsec protocols at the network layer.

## Key Features

### 1. VPN Setup
Configured a site-to-site IPsec VPN from R1 to R2, where R2 acts as a pass-through router. This means that R2 does not decrypt or inspect the VPN traffic but simply forwards it between the two VPN endpoints (R1 and R2).

### 2. Enabling Security Package
To enable VPN features, the Security Technology package was activated on both routers using the following steps:
1. Checked the license status:
   ```
   show version
   ```
2. Enabled the security package if necessary:
   ```
   license boot module c1900 technology-package securityk9
   ```
3. Accepted the license agreement, saved the configuration, and reloaded the routers:
   ```
   write memory
   reload
   ```

### 3. Traffic Identification with ACLs
Defined interesting traffic that should be encrypted using Access Control Lists (ACL 110) on both routers. This traffic triggers the VPN tunnel:
- On R1:
  ```
  access-list 110 permit ip 172.151.100.0 0.0.0.255 172.127.20.0 0.0.0.255
  ```
- On R2:
  ```
  access-list 110 permit ip 172.127.20.0 0.0.0.255 172.151.100.0 0.0.0.255
  ```

### 4. ISAKMP Phase 1 Configuration
Defined the ISAKMP (Internet Security Association and Key Management Protocol) settings to establish secure peer authentication and key exchange:
``` 
crypto isakmp policy 10
 encryption aes 256
 authentication pre-share
 group 5
exit
crypto isakmp key cybersec1 address <peer-ip>
```

### 5. IPsec Phase 2 Configuration
Configured the IPsec settings to define how data will be encrypted and authenticated:
1. Defined the transform set:
   ```
   crypto ipsec transform-set  R1-R3 esp-aes esp-sha-hmac
   ```
2. Created the crypto map and applied it to the interface:
   ```
   crypto map CMAP 10 ipsec-isakmp
   set peer <peer-ip>
   set transform-set VPN-SET
   match address 110
   exit
   interface <interface>
   crypto map CMAP
   ```
   #### Attention
      The same goes for the second peer but notice that the interfaces and networks are different.

### 6. File Upload and Analysis
To verify the VPN functionality, a file was uploaded from the R1 LAN to the FTP server in R2's LAN. The FTP server was configured with the password `cisco12345`. A sniffer tool was then used to analyze the traffic and confirm that the transfer occurred securely over the VPN tunnel.

### 7. Map and Credentials
- **Crypto Map Name:** `CMAP`
- **FTP Server Password:** `cisco12345`
- **SSH Username/Password:** `admin/cisco12345`
- **Console Password:** `cisco12345`
- **VTY Password:** `cisco12345`
- **Enable Password:** `cisco12345`
- **OSPF Process ID:** `1`

## Summary
This VPN configuration project successfully established a site-to-site IPsec VPN between R1 and R2. The configuration involved setting up ISAKMP and IPsec phases, creating and applying crypto maps, and identifying interesting traffic through ACLs. The secure file transfer and subsequent analysis validated the effectiveness of the VPN tunnel.





