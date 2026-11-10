# NetArchitect Generated Cisco IOS Configurations & Simulation Testbed

**Author**: Tanmay Aggarwal  
**Enrollment No**: 00714802724  
**Course**: CIC-307 Computer Networks  
**Target Hardware / Emulators**: Cisco Catalyst 3560, Catalyst 2960, Cisco 2911 ISR / Cisco Packet Tracer 8.x / GNS3  

---

## Overview

This directory contains the production-hardened **Cisco IOS configuration templates** synthesized by the NetArchitect engine for the 3 preset reference network architectures:
1. **Campus LAN** (`campus-lan/`, `campus_lan_config.txt`, `campus_lan_configs.zip`)
2. **Metro MAN** (`metro-man/`, `metro_man_config.txt`, `metro_man_configs.zip`)
3. **Global WAN** (`global-wan/`, `global_wan_config.txt`, `global_wan_configs.zip`)

Each profile includes individual device CLI configuration files, a concatenated all-in-one script, and an exportable ZIP archive.

---

## Directory Layout

```text
configs/
├── README.md                      # Hardware configuration & loading guide (this document)
├── VERIFICATION_REPORT.md         # Formal Packet Tracer & GNS3 simulation test report
│
├── campus-lan/                    # Enterprise Campus LAN (3 Buildings, 1,600 Endpoints)
│   ├── layer3_switch.txt          # Catalyst 3560 Distribution Switch (SVIs, OSPF Area 0, ACLs)
│   ├── access_switch.txt          # Catalyst 2960 Access Switch (PortFast, BPDU Guard, Native VLAN)
│   └── wan_router.txt             # Cisco 2911 ISR WAN Edge Router (Transit link, OSPF)
├── campus_lan_config.txt          # Concatenated Campus LAN configuration
├── campus_lan_configs.zip         # Pre-packaged ZIP bundle for export
│
├── metro-man/                     # Metropolitan Area Network (3 Sites, EVPL, Low Latency)
│   ├── layer3_switch.txt          # Metro Distribution Switch (SVIs, Multi-Area OSPF)
│   ├── access_switch.txt          # Metro Access Switch (Hardened L2 edge)
│   └── wan_router.txt             # Metro Edge Router (EVPL QinQ, DSCP EF QoS policies)
├── metro_man_config.txt           # Concatenated Metro MAN configuration
├── metro_man_configs.zip          # Pre-packaged ZIP bundle for export
│
└── global-wan/                    # Global Multi-Site WAN (5 Locations, Cloud VPC, IPsec)
    ├── layer3_switch.txt          # Core Distribution Switch
    ├── access_switch.txt          # Branch Access Switch
    └── wan_router.txt             # Border Router (eBGP AS 65010, AES-256 IKEv2 IPsec VPN)
├── global_wan_config.txt          # Concatenated Global WAN configuration
└── global_wan_configs.zip         # Pre-packaged ZIP bundle for export
```

---

## Device Role Matrix

| Device Role | Hardware Model | Operating System | Primary Configured Capabilities |
|:---|:---|:---|:---|
| **Layer 3 Distribution Switch** (`*-DIST-01`) | Cisco Catalyst 3560-24PS | Cisco IOS 15.0(2)SE4 (IP Services) | `ip routing`, SVIs with default gateway addresses, OSPF routing, 802.1Q trunking, Native VLAN 4094, SSH v2, Extended IPv4 Security ACLs with IoT isolation. |
| **Layer 2 Access Switch** (`*-ACCESS-01`) | Cisco Catalyst 2960-24TT | Cisco IOS 15.0(2)SE4 (LAN Base) | Rapid-PVST+, PortFast, BPDU Guard, 802.1Q trunk uplinks, restricted `switchport trunk allowed vlan`, unused port shutdown. |
| **Edge WAN Router** (`*-EDGE-01`) | Cisco 2911 / 4321 ISR | Cisco IOS 15.1(4)M4 (IP Base / SEC-K9) | OSPF routing, eBGP autonomous border peering (AS 65010), DSCP EF real-time QoS priority queuing, IKEv2 / IPsec site-to-site VPN proposals. |

---

## How to Load Configurations into Cisco Packet Tracer or GNS3

### Step 1: Deploy Devices in Topology
1. In Cisco Packet Tracer or GNS3, drag onto the canvas:
   - One **Cisco 2911** or **4321** Router
   - One **Catalyst 3560-24PS** Multilayer Switch
   - One **Catalyst 2960-24TT** Switch
2. Connect interfaces:
   - Router `GigabitEthernet0/0` $\longleftrightarrow$ Catalyst 3560 `GigabitEthernet1/0/1`
   - Catalyst 3560 `GigabitEthernet1/0/48` $\longleftrightarrow$ Catalyst 2960 `GigabitEthernet0/48`
   - Connect client PCs / IoT endpoints to Catalyst 2960 `FastEthernet0/1` – `0/4`.

### Step 2: Apply Device Configurations
1. Open the device CLI tab or console window.
2. Enter privileged EXEC mode:
   ```cisco
   enable
   configure terminal
   ```
3. Open the corresponding configuration file (e.g., `campus-lan/layer3_switch.txt`).
4. Copy the entire file content and paste it into the console session.
5. Exit configuration mode and save to NVRAM:
   ```cisco
   end
   copy running-config startup-config
   ```

---

## Verification & Testing Evidence

For comprehensive packet-level verification, route table outputs (`show ip route`), trunking status (`show interfaces trunk`), and ACL match logs, refer to the formal verification report:
- **Formal Verification Report**: [`VERIFICATION_REPORT.md`](file:///home/tanmay/Downloads/netarchitecture/netarchitecture/configs/VERIFICATION_REPORT.md)
