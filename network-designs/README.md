# Network Design — Nexarion Solutions

**Project Type:** Secure Network Architecture Design  
**Scenario:** Redesigning Nexarion Solutions' office network to implement proper segmentation, VLANs, and security controls  
**Security+ Domains:** Architecture and Design | Implementation

---

## Scenario Background

Nexarion Solutions' existing network was a flat network — every device (workstations, servers, printers, IP cameras, guest Wi-Fi) was on the same subnet (`192.168.1.0/24`). This meant that a compromised workstation had direct network access to the file servers and management interfaces. The IT Security team was tasked with redesigning the network.

**Company size:** ~200 employees across 3 departments (General Office, Legal, Accounting)  
**Location:** Single office, 3 floors  
**Internet uplink:** 1 Gbps fibre via ISP  
**Key assets:** File servers, domain controller, client data (healthcare + legal)

---

## Design Goals

1. Separate network segments for different user groups and device types
2. Restrict inter-VLAN traffic using firewall rules (zero-trust default)
3. Isolate guest Wi-Fi from the internal network entirely
4. Protect management interfaces (servers, switches, firewalls) from user devices
5. Enable centralised logging of all inter-VLAN traffic

---

## VLAN Design

| VLAN ID | Name | Subnet | Description |
|---------|------|--------|-------------|
| 10 | General Staff | `10.10.10.0/24` | Standard employee workstations |
| 20 | Legal | `10.10.20.0/24` | Legal department workstations (elevated sensitivity) |
| 30 | Accounting | `10.10.30.0/24` | Accounting workstations (financial data) |
| 40 | Servers | `10.10.40.0/24` | All internal servers (file, DC, email) |
| 50 | Management | `10.10.50.0/24` | Network device management (switches, firewall, AP controllers) |
| 60 | Security Cameras | `10.10.60.0/24` | CCTV and access control systems |
| 70 | Printers | `10.10.70.0/24` | Network printers and MFDs |
| 80 | Guest Wi-Fi | `10.10.80.0/24` | Visitor internet access — no internal access |
| 90 | DMZ | `10.10.90.0/24` | Internet-facing services (web server, email gateway) |

---

## Network Diagram (Text Representation)

```
┌────────────────────────────────────────────────────────────────┐
│                        INTERNET (ISP)                          │
└──────────────────────────┬─────────────────────────────────────┘
                           │ 1 Gbps Fibre
                           ▼
┌──────────────────────────────────────────────────────────────────┐
│                  PERIMETER FIREWALL                              │
│              (Fortinet FortiGate 200F)                           │
│                                                                  │
│  Zones: WAN | DMZ | LAN | Management                            │
└──┬───────────────┬───────────────────────┬───────────────────────┘
   │               │                       │
   ▼               ▼                       ▼
┌──────┐     ┌──────────┐          ┌───────────────┐
│ DMZ  │     │  Core    │          │  Management   │
│VLAN90│     │  L3      │          │  VLAN 50      │
│      │     │ Switch   │          │               │
│Web01 │     │(Cisco    │          │ Switch mgmt   │
│Mail  │     │ 3850)    │          │ Firewall mgmt │
│Gateway     └──┬───────┘          │ AP Controller │
└──────┘        │                  └───────────────┘
           ┌────┴──────────────────────────────────┐
           │  Inter-VLAN routing controlled by FW  │
           └──┬──────┬──────┬──────┬──────┬────────┘
              │      │      │      │      │
           VLAN10  VLAN20 VLAN30 VLAN40 VLAN70
           General Legal  Acctg  Servers Printers
                                  │
                              ┌───┴──────────────┐
                              │  NEXARION-DC01   │
                              │  NEXARION-FS01   │
                              │  NEXARION-FS02   │
                              │  NEXARION-WEB01  │
                              └──────────────────┘

           VLAN60        VLAN80
           Cameras       Guest Wi-Fi
           (isolated)    (internet only)
```

---

## Firewall Rule Policy (Inter-VLAN)

The firewall uses a **default-deny** policy. Traffic is only permitted where explicitly required.

### Inbound from Internet (WAN → DMZ)

| Source | Destination | Port | Protocol | Action | Purpose |
|--------|-------------|------|----------|--------|---------|
| Any | NEXARION-WEB01 (10.10.90.10) | 443 | TCP | PERMIT | HTTPS web traffic |
| Any | Mail Gateway (10.10.90.20) | 25 | TCP | PERMIT | Inbound email (SMTP) |
| Any | Any | Any | Any | DENY + LOG | Default deny |

### Inbound from Internet (WAN → Internal)

| Source | Destination | Port | Protocol | Action |
|--------|-------------|------|----------|--------|
| Any | Any Internal | Any | Any | DENY + LOG |

No direct inbound traffic from the internet to internal VLANs. Remote access is via VPN only.

### VPN Access (Remote Users → Internal)

| Source | Destination | Port | Action | Notes |
|--------|-------------|------|--------|-------|
| VPN Pool (10.10.100.0/24) | VLAN 10/20/30 | Required ports only | PERMIT | MFA required |
| VPN Pool | VLAN 40 (Servers) | 445, 3389 | PERMIT | File and RDP access |
| VPN Pool | Any | Any | DENY + LOG | All other traffic blocked |

### Inter-VLAN Rules (Internal Segmentation)

| Source VLAN | Destination VLAN | Port | Action | Reason |
|-------------|-----------------|------|--------|--------|
| VLAN 10 (General) | VLAN 40 (Servers) | 445 (SMB), 636 (LDAPS) | PERMIT | File share + directory |
| VLAN 20 (Legal) | VLAN 40 (Servers) | 445, 636 | PERMIT | File share + directory |
| VLAN 30 (Accounting) | VLAN 40 (Servers) | 445, 636 | PERMIT | File share + directory |
| VLAN 10/20/30 | VLAN 70 (Printers) | 9100, 631 | PERMIT | Printing |
| VLAN 40 (Servers) | VLAN 50 (Management) | 514 (Syslog) | PERMIT | Log forwarding to SIEM |
| VLAN 10/20/30 | VLAN 50 (Management) | Any | DENY + LOG | Users cannot reach mgmt |
| VLAN 60 (Cameras) | Any | Any | DENY + LOG | Cameras fully isolated |
| VLAN 80 (Guest) | Any Internal | Any | DENY + LOG | Guest internet only |
| VLAN 20 (Legal) | VLAN 10/30 | Any | DENY + LOG | Legal data stays isolated |
| VLAN 30 (Accounting) | VLAN 10/20 | Any | DENY + LOG | Accounting data isolated |
| Any | Any | Any | DENY + LOG | Default deny all |

---

## DNS and DHCP Design

| VLAN | DNS Servers | DHCP Server | Notes |
|------|-------------|-------------|-------|
| VLAN 10/20/30 | 10.10.40.10 (DC01), 10.10.40.11 (DC02) | DC01 | Internal DNS only |
| VLAN 80 (Guest) | 8.8.8.8, 1.1.1.1 | Firewall DHCP | Public DNS — no internal resolution |
| VLAN 90 (DMZ) | 8.8.8.8 | Static IPs | DMZ servers have static IPs |

---

## Wi-Fi Design

| SSID | VLAN | Authentication | Encryption | Notes |
|------|------|---------------|------------|-------|
| `NEXARION-CORP` | VLAN 10 | 802.1X (domain credentials) | WPA3-Enterprise | Staff devices |
| `NEXARION-SECURE` | VLAN 20/30 | 802.1X + device certificate | WPA3-Enterprise | Legal/Accounting laptops |
| `NEXARION-GUEST` | VLAN 80 | Captive portal (terms acceptance) | WPA3-Personal | Visitors only |

---

## Remote Access (VPN)

- **VPN Type:** SSL VPN (Fortinet FortiClient)
- **Authentication:** Username + Password + TOTP (Microsoft Authenticator)
- **Split Tunnelling:** Disabled — all traffic routed through VPN when connected
- **VPN Pool:** `10.10.100.0/24` (separate VLAN, restricted by firewall rules above)
- **VPN Session Logging:** All connections logged to SIEM

---

## Physical Security Considerations

- Server room access: Badge reader + PIN, CCTV coverage
- Network switches in locked comms rooms on each floor
- All patch panels labelled and documented
- Rogue device detection: 802.1X port authentication on all wired switch ports

---

## Security+ Concepts Demonstrated

- **Network segmentation** — VLANs separating users, servers, and devices
- **Defence in depth** — multiple controls (VLAN + firewall + 802.1X)
- **Least privilege** — cameras isolated, guest has no internal access
- **DMZ** — internet-facing services separated from internal network
- **Zero-trust model** — default-deny firewall with explicit allow rules
- **VPN and remote access** — MFA-protected, no split tunnelling
- **802.1X** — port-based network access control
- **Logging** — all denied traffic logged for incident investigation

---

## Reflection

Designing this network from scratch was challenging but very rewarding. Before this project, I thought of VLANs as just a way to organise the network — I didn't realise they were a security control. Seeing how a flat network gives a compromised workstation direct access to the domain controller made the risk very concrete. The firewall rule design was the most complex part; writing default-deny rules and then building exceptions made me think carefully about what each device actually needs to communicate with. I also learned why guest Wi-Fi should always be completely isolated — even well-intentioned guest devices could carry malware.
