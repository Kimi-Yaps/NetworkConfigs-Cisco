# **Cisco Networking Reference: ACL & OSPF**

This guide provides the essential command syntax for implementing **Access Control Lists (ACLs)** and **Open Shortest Path First (OSPF)** on Cisco IOS routers.

## ---

**1\. Access Control Lists (ACL)**

ACLs are used for packet filtering and traffic identification. Remember: **There is an implicit "deny all" at the end of every ACL.**

### **A. Numbered ACLs**

Standard ACLs filter by source IP only, while Extended ACLs filter by source, destination, protocol, and port.

Cisco CLI

\! \--- Standard ACL (1-99, 1300-1999) \---  
access-list 10 permit 192.168.1.0 0.0.0.255  
access-list 10 remark Permit Guest WiFi Network

\! \--- Extended ACL (100-199, 2000-2699) \---  
\! Syntax: access-list \<num\> \<action\> \<proto\> \<src\> \<src-wc\> \<dst\> \<dst-wc\> \[eq \<port\>\]  
access-list 100 deny tcp 10.0.0.0 0.255.255.255 any eq 80  
access-list 100 permit ip any any

\! \--- Application \---  
interface GigabitEthernet0/0  
 ip access-group 100 in

### **B. Named ACLs**

Named ACLs are preferred in modern networks because they allow for the deletion of specific lines without recreating the entire list.

Cisco CLI

\! \--- Standard Named \---  
ip access-list standard SECURE\_VLAN  
 permit 192.168.10.0 0.0.0.255  
 deny any

\! \--- Extended Named \---  
ip access-list extended REMOTE\_ACCESS  
 permit tcp 192.168.1.0 0.0.0.255 any eq 22  
 deny ip any any log

\! \--- Application \---  
interface fa0/0  
 ip access-group REMOTE\_ACCESS in

## 

## 

## 

## 

## 

## 

## 

## 

## 

## **2\. OSPF Configuration**

OSPF is a Link-State routing protocol. It uses the **Shortest Path First (SPF)** algorithm to determine the best route.

### **A. Basic Setup & Process**

Cisco CLI

router ospf 1  
 router-id 1.1.1.1  
 network 192.168.1.0 0.0.0.255 area 0  
 network 10.0.0.0 0.0.0.3 area 0  
 passive-interface GigabitEthernet0/1  \! Prevents OSPF updates on user-facing LAN

### **B. Interface Tuning & Authentication**

Tuning allows you to influence the Designated Router (DR) election and path selection.

Cisco CLI

interface GigabitEthernet0/0  
 ip ospf cost 10                       \! Manually sets path cost  
 ip ospf priority 255                  \! Ensures this router becomes DR  
 ip ospf hello-interval 5              \! Default is usually 10  
 ip ospf message-digest-key 1 md5 Pass123  
   
\! \--- Enabling MD5 globally for Area 0 \---  
router ospf 1  
 area 0 authentication message-digest

## 

## 

## 

## **3\. Verification & Troubleshooting**

Use these commands to confirm your configuration is working as intended.

### **ACL Verification**

| Command | Description |
| :---- | :---- |
| show access-lists | Displays all ACLs and hit counts (matches). |
| show ip access-lists | Specific to IPv4 ACLs. |
| \`show ip interface | include access\` |

### 

### 

### **OSPF Verification**

| Command | Description |
| :---- | :---- |
| show ip ospf neighbor | **Crucial:** Checks if adjacencies are in FULL state. |
| show ip ospf interface brief | Verifies which interfaces are participating in OSPF. |
| show ip route ospf | Displays only the routes learned via OSPF. |
| clear ip ospf process | Restarts the OSPF engine (required after changing Router ID). |

