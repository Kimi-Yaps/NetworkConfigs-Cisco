# Network Design – IP Address Tables

---

## Network Overview

| Device | Role |
|--------|------|
| R1 | Core Router – Site A |
| R2 | Distribution Router – Site B |
| R3 | Edge Router – Site C |
| SW1 | Layer 3 Switch – VLAN trunk |
| PC-A to PC-F | End hosts |

---

## Topic 01 – Block/Allow Ping Between Subnets with Standard ACL
**Dates: 28 Feb – 1 March**

### Requirements
- Two subnets that can/cannot ping each other based on standard ACL
- ACL applied on router interface (inbound/outbound)
- One subnet ALLOWED, one DENIED

### IP Address Table

| Device | Interface | IP Address | Subnet Mask | CIDR | Gateway |
|--------|-----------|------------|-------------|------|---------|
| R1 | Gi0/0 (to SW1) | 10.10.1.1 | 255.255.255.128 | /25 | — |
| R1 | Gi0/1 (to R2) | 172.16.0.1 | 255.255.255.255 | /32 | — |
| R2 | Gi0/0 (to SW2) | 10.10.2.1 | 255.255.255.192 | /26 | — |
| R2 | Gi0/1 (to R1) | 172.16.0.2 | 255.255.255.255 | /32 | — |
| PC-A | NIC | 10.10.1.10 | 255.255.255.128 | /25 | 10.10.1.1 |
| PC-B | NIC | 10.10.1.20 | 255.255.255.128 | /25 | 10.10.1.1 |
| PC-C | NIC | 10.10.2.10 | 255.255.255.192 | /26 | 10.10.2.1 |
| PC-D | NIC | 10.10.2.20 | 255.255.255.192 | /26 | 10.10.2.1 |

### Standard ACL Rule (Applied on R1 Gi0/1 – Outbound)
```
access-list 10 deny   10.10.2.0 0.0.0.63   ! Block Site B subnet from pinging Site A
access-list 10 permit any
```

---

## Topic 02 – Permit HTTP from One Host, Deny Others (Extended ACL)
**Dates: 2 March – 3 March**

### Requirements
- One specific host permitted HTTP (port 80) to server
- All other hosts denied HTTP
- Extended ACL (source + destination + port)

### IP Address Table

| Device | Interface | IP Address | Subnet Mask | CIDR | Gateway |
|--------|-----------|------------|-------------|------|---------|
| R1 | Gi0/0 (LAN) | 10.20.1.1 | 255.255.255.224 | /27 | — |
| R1 | Gi0/1 (to R2) | 172.16.1.1 | 255.255.255.255 | /32 | — |
| R2 | Gi0/0 (Server LAN) | 10.20.2.1 | 255.255.255.224 | /27 | — |
| R2 | Gi0/1 (to R1) | 172.16.1.2 | 255.255.255.255 | /32 | — |
| PC-A (Permitted) | NIC | 10.20.1.5 | 255.255.255.224 | /27 | 10.20.1.1 |
| PC-B (Denied) | NIC | 10.20.1.10 | 255.255.255.224 | /27 | 10.20.1.1 |
| PC-C (Denied) | NIC | 10.20.1.15 | 255.255.255.224 | /27 | 10.20.1.1 |
| Web Server | NIC | 10.20.2.10 | 255.255.255.224 | /27 | 10.20.2.1 |

### Extended ACL Rule (Applied on R1 Gi0/0 – Inbound)
```
access-list 110 permit tcp host 10.20.1.5 host 10.20.2.10 eq 80
access-list 110 deny   tcp any           host 10.20.2.10 eq 80
access-list 110 permit ip any any
```

---

## Topic 03 – Deny Telnet/SSH, Allow Specific IPs
**Dates: 4 March – 5 March**

### Requirements
- Block Telnet (port 23) and SSH (port 22) from all except admin host
- Admin host IP specifically permitted
- Extended ACL protecting VTY lines

### IP Address Table

| Device | Interface | IP Address | Subnet Mask | CIDR | Gateway |
|--------|-----------|------------|-------------|------|---------|
| R1 | Gi0/0 (Admin LAN) | 10.30.1.1 | 255.255.255.192 | /26 | — |
| R1 | Gi0/1 (to R2) | 172.16.2.1 | 255.255.255.255 | /32 | — |
| R2 | Gi0/0 (User LAN) | 10.30.2.1 | 255.255.255.128 | /25 | — |
| R2 | Gi0/1 (to R1) | 172.16.2.2 | 255.255.255.255 | /32 | — |
| Admin PC | NIC | 10.30.1.5 | 255.255.255.192 | /26 | 10.30.1.1 |
| User PC-1 | NIC | 10.30.2.10 | 255.255.255.128 | /25 | 10.30.2.1 |
| User PC-2 | NIC | 10.30.2.20 | 255.255.255.128 | /25 | 10.30.2.1 |
| R2 Loopback0 | Lo0 | 10.30.0.1 | 255.255.255.255 | /32 | — |

### ACL Rule (Applied on VTY lines of R2)
```
access-list 120 permit tcp host 10.30.1.5 any eq 22
access-list 120 permit tcp host 10.30.1.5 any eq 23
access-list 120 deny   tcp any any eq 22
access-list 120 deny   tcp any any eq 23
access-list 120 permit ip any any

line vty 0 4
 access-class 120 in
```

---

## Topic 04 – IPv6 ACL to Filter VLAN Traffic
**Dates: 6 March – 7 March**

### Requirements
- IPv6 addresses assigned per VLAN
- IPv6 ACL blocks traffic between specific VLANs
- Applied on Layer 3 Switch SVI interfaces

### IPv6 Address Table

| Device | Interface / VLAN | IPv6 Address | Prefix | Gateway |
|--------|-----------------|--------------|--------|---------|
| SW1 (L3) | VLAN 10 SVI | 2001:DB8:A:10::1 | /64 | — |
| SW1 (L3) | VLAN 20 SVI | 2001:DB8:A:20::1 | /64 | — |
| SW1 (L3) | VLAN 30 SVI | 2001:DB8:A:30::1 | /64 | — |
| R1 | Gi0/0 (uplink) | 2001:DB8:A:FF::1 | /64 | — |
| PC-VLAN10-A | NIC | 2001:DB8:A:10::10 | /64 | 2001:DB8:A:10::1 |
| PC-VLAN10-B | NIC | 2001:DB8:A:10::20 | /64 | 2001:DB8:A:10::1 |
| PC-VLAN20-A | NIC | 2001:DB8:A:20::10 | /64 | 2001:DB8:A:20::1 |
| PC-VLAN30-A | NIC | 2001:DB8:A:30::10 | /64 | 2001:DB8:A:30::1 |

### IPv6 ACL Rule (Block VLAN10 → VLAN20)
```
ipv6 access-list BLOCK_V10_TO_V20
 deny   ipv6 2001:DB8:A:10::/64 2001:DB8:A:20::/64
 permit ipv6 any any

interface vlan 10
 ipv6 traffic-filter BLOCK_V10_TO_V20 out
```

---

## Topic 05 – Troubleshoot Broken ACL (Wrong Direction/Order)
**Dates: 8 March – 10 March**

### Requirements
- Intentionally misconfigured ACL (wrong interface direction or rule order)
- Student must identify and fix the issue
- Document: broken config → diagnosis → fixed config

### IP Address Table

| Device | Interface | IP Address | Subnet Mask | CIDR | Gateway |
|--------|-----------|------------|-------------|------|---------|
| R1 | Gi0/0 (LAN-A) | 10.40.1.1 | 255.255.255.224 | /27 | — |
| R1 | Gi0/1 (to R2) | 172.16.3.1 | 255.255.255.255 | /32 | — |
| R2 | Gi0/0 (LAN-B) | 10.40.2.1 | 255.255.255.192 | /26 | — |
| R2 | Gi0/1 (to R1) | 172.16.3.2 | 255.255.255.255 | /32 | — |
| PC-A | NIC | 10.40.1.5 | 255.255.255.224 | /27 | 10.40.1.1 |
| PC-B | NIC | 10.40.1.10 | 255.255.255.224 | /27 | 10.40.1.1 |
| Server-1 | NIC | 10.40.2.5 | 255.255.255.192 | /26 | 10.40.2.1 |

### Broken Config (for troubleshooting exercise)
```
! WRONG: ACL applied outbound instead of inbound; permit before deny
access-list 130 permit ip any any
access-list 130 deny   ip 10.40.1.0 0.0.0.31 host 10.40.2.5

interface Gi0/0
 ip access-group 130 out   ! WRONG direction
```

### Fixed Config
```
access-list 130 deny   ip 10.40.1.0 0.0.0.31 host 10.40.2.5
access-list 130 permit ip any any

interface Gi0/0
 ip access-group 130 in    ! CORRECT direction
```

---

## Topic 06 – Meet 5+ Requirements (OSPF Exempt, Block Ports)
**Dates: 11 March – 13 March**

### Requirements (Must meet ALL 5+)
1. ✅ OSPF routing traffic exempted from ACL filtering
2. ✅ Block specific TCP ports (e.g., 8080, 445)
3. ✅ Allow only one subnet to reach DMZ
4. ✅ Deny ICMP from untrusted zone
5. ✅ Permit management traffic only from admin subnet
6. ✅ (Bonus) Rate-limit or log denied entries

### IP Address Table

| Device | Interface | IP Address | Subnet Mask | CIDR | Zone | Gateway |
|--------|-----------|------------|-------------|------|------|---------|
| R1 | Gi0/0 (Trust LAN) | 10.50.1.1 | 255.255.255.128 | /25 | Trust | — |
| R1 | Gi0/1 (to R2) | 172.16.4.1 | 255.255.255.255 | /32 | Core | — |
| R1 | Gi0/2 (DMZ) | 10.50.3.1 | 255.255.255.224 | /27 | DMZ | — |
| R2 | Gi0/0 (Untrust LAN) | 10.50.2.1 | 255.255.255.192 | /26 | Untrust | — |
| R2 | Gi0/1 (to R1) | 172.16.4.2 | 255.255.255.255 | /32 | Core | — |
| R1 Lo0 | Loopback | 10.50.0.1 | 255.255.255.255 | /32 | Mgmt | — |
| R2 Lo0 | Loopback | 10.50.0.2 | 255.255.255.255 | /32 | Mgmt | — |
| Admin PC | NIC | 10.50.1.5 | 255.255.255.128 | /25 | Trust | 10.50.1.1 |
| User PC-1 | NIC | 10.50.1.10 | 255.255.255.128 | /25 | Trust | 10.50.1.1 |
| Untrust PC | NIC | 10.50.2.10 | 255.255.255.192 | /26 | Untrust | 10.50.2.1 |
| DMZ Server | NIC | 10.50.3.10 | 255.255.255.224 | /27 | DMZ | 10.50.3.1 |

### Multi-Requirement ACL (Applied on R1 Gi0/1 Inbound)
```
access-list 150 permit ospf any any                          ! Req 1: Allow OSPF
access-list 150 deny   tcp any any eq 8080                   ! Req 2: Block port 8080
access-list 150 deny   tcp any any eq 445                    ! Req 2: Block SMB
access-list 150 permit ip 10.50.1.0 0.0.0.127 10.50.3.0 0.0.0.31  ! Req 3: Trust→DMZ
access-list 150 deny   icmp 10.50.2.0 0.0.0.63 any          ! Req 4: Deny ICMP untrust
access-list 150 permit tcp host 10.50.1.5 any eq 22          ! Req 5: Admin SSH only
access-list 150 deny   tcp any any eq 22
access-list 150 permit ip any any log                        ! Req 6: Log remaining

interface Gi0/1
 ip access-group 150 in
```

---

## 🔗 Router-to-Router Link Summary (/32 Point-to-Point)

| Link | R1 Side (/32) | R2 Side (/32) | Topic |
|------|--------------|--------------|-------|
| R1 ↔ R2 (T01) | 172.16.0.1/32 | 172.16.0.2/32 | 01 |
| R1 ↔ R2 (T02) | 172.16.1.1/32 | 172.16.1.2/32 | 02 |
| R1 ↔ R2 (T03) | 172.16.2.1/32 | 172.16.2.2/32 | 03 |
| R1 ↔ R2 (T05) | 172.16.3.1/32 | 172.16.3.2/32 | 05 |
| R1 ↔ R2 (T06) | 172.16.4.1/32 | 172.16.4.2/32 | 06 |

---

## Subnet Reference Summary

| Network | CIDR | Subnet Mask | Usable Hosts | Used In Topic |
|---------|------|-------------|--------------|---------------|
| 10.10.1.0 | /25 | 255.255.255.128 | 126 | 01 |
| 10.10.2.0 | /26 | 255.255.255.192 | 62 | 01 |
| 10.20.1.0 | /27 | 255.255.255.224 | 30 | 02 |
| 10.20.2.0 | /27 | 255.255.255.224 | 30 | 02 |
| 10.30.1.0 | /26 | 255.255.255.192 | 62 | 03 |
| 10.30.2.0 | /25 | 255.255.255.128 | 126 | 03 |
| 10.40.1.0 | /27 | 255.255.255.224 | 30 | 05 |
| 10.40.2.0 | /26 | 255.255.255.192 | 62 | 05 |
| 10.50.1.0 | /25 | 255.255.255.128 | 126 | 06 |
| 10.50.2.0 | /26 | 255.255.255.192 | 62 | 06 |
| 10.50.3.0 | /27 | 255.255.255.224 | 30 | 06 |
| 172.16.x.x | /32 | 255.255.255.255 | Point-to-Point | All |

---
