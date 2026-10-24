# Subnetting and VLSM Design

## Goal

NexaTech uses the private address block `10.10.0.0/16` as the overall internal addressing plan. Rather than placing every VLAN in a same-size network, the plan divides that space into smaller subnets using **subnetting** and **Variable Length Subnet Masking (VLSM)**. The different prefix lengths provide different host capacities for department, management, server, and infrastructure networks.

## Prefix sizes used

| Prefix | Subnet mask | Total addresses | Usable hosts | Typical role in this project |
|---|---|---:|---:|---|
| `/27` | `255.255.255.224` | 32 | 30 | Engineering (largest user subnet) |
| `/28` | `255.255.255.240` | 16 | 14 | HR, Finance, IT, Sales & Marketing, Guest, Network Management |
| `/29` | `255.255.255.248` | 8 | 6 | Management and Server VLANs |
| `/30` | `255.255.255.252` | 4 | 2 | Routed point-to-point links |

Usable-host counts exclude the network and broadcast addresses. The configured default gateway also consumes one usable address in each VLAN subnet.

## VLAN allocation

| VLAN | Name | Network/CIDR | Subnet mask | Usable host range | Broadcast | Default gateway |
|---:|---|---|---|---|---|---|
| 40 | Engineering | `10.10.0.0/27` | `255.255.255.224` | `10.10.0.1–10.10.0.30` | `10.10.0.31` | `10.10.0.30` |
| 50 | Sales & Marketing | `10.10.0.32/28` | `255.255.255.240` | `10.10.0.33–10.10.0.46` | `10.10.0.47` | `10.10.0.46` |
| 80 | Guest | `10.10.0.48/28` | `255.255.255.240` | `10.10.0.49–10.10.0.62` | `10.10.0.63` | `10.10.0.62` |
| 20 | HR | `10.10.0.64/28` | `255.255.255.240` | `10.10.0.65–10.10.0.78` | `10.10.0.79` | `10.10.0.78` |
| 30 | Finance | `10.10.0.80/28` | `255.255.255.240` | `10.10.0.81–10.10.0.94` | `10.10.0.95` | `10.10.0.94` |
| 60 | IT | `10.10.0.96/28` | `255.255.255.240` | `10.10.0.97–10.10.0.110` | `10.10.0.111` | `10.10.0.110` |
| 10 | Management | `10.10.0.112/29` | `255.255.255.248` | `10.10.0.113–10.10.0.118` | `10.10.0.119` | `10.10.0.118` |
| 70 | Servers | `10.10.0.120/29` | `255.255.255.248` | `10.10.0.121–10.10.0.126` | `10.10.0.127` | `10.10.0.126` |
| 99 | Network Management | `10.10.0.128/28` | `255.255.255.240` | `10.10.0.129–10.10.0.142` | `10.10.0.143` | `10.10.0.142` |
| — | CORE–EDGE transit | `10.10.0.144/30` | `255.255.255.252` | `10.10.0.145–10.10.0.146` | `10.10.0.147` | — |
| — | EDGE–ASA inside transit | `10.10.0.148/30` | `255.255.255.252` | `10.10.0.149–10.10.0.150` | `10.10.0.151` | — |
| 999 | Native / Unused | No SVI subnet | — | — | — | — |

## Why these sizes?

- **`/27` for Engineering:** gives up to 30 usable addresses, the largest VLAN allocation in this plan.
- **`/28` for typical VLANs:** gives 14 usable addresses for the departments/guest segments sized at this capacity.
- **`/29` for Management and Servers:** Each subnet provides six usable host addresses. The Server VLAN uses four server addresses and one gateway address, leaving one usable address available. The Management VLAN provides capacity for a small number of management devices.
- **`/30` for point-to-point links:** provides two usable IP addresses, one for each end of a routed link.

Host capacities reflect the subnet design, not actual employee counts. Reassess subnet sizes before expanding the network. The external simulation uses `203.0.113.4/30` for the ASA-to-ISP transit link and `198.51.100.0/24` for the ISP-to-INTERNET-SRV network. The public NAT block `203.0.113.8/29`, which contains `203.0.113.10`, is routed through the ASA as described in [Network Design](network-design.md).