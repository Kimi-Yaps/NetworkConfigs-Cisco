# Topic 05 – Troubleshoot Broken ACL (Wrong Direction/Order)
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