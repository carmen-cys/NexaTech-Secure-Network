# Network Design

## Overview

NexaTech represents a fictional technology company operating across three office floors. Access switches connect user devices, while a multilayer core switch provides SVIs and inter-VLAN routing. An edge router connects the internal network to an ASA firewall, which separates the internal network from a simulated ISP and Internet Server.

## Logical topology

The overall logical topology of NexaTech is shown below.

![NexaTech Overall Logical Topology](../screenshots/topology-overview.png)

The high-level traffic path is:

`Departmental PCs → Access switches → CORE-SW → EDGE-R1 → ASA → ISP → INTERNET-SRV`

Detailed views of the individual floors and the edge-security path are available below:

- [Edge and Security Path](../screenshots/edge-security.png)
- [Floor 1](../screenshots/floor1.png)
- [Floor 2](../screenshots/floor2.png)
- [Floor 3](../screenshots/floor3.png)


## Subnetting and VLSM

The internal allocation uses `10.10.0.0/16` as its private address plan and subdivides it with VLSM. Prefix lengths are selected according to the role and expected scale of each segment: `/27` for Engineering, `/28` for typical departmental and guest segments, `/29` for the smaller Management and Server VLANs, and `/30` for point-to-point routed links. The intent is to allocate appropriately sized networks instead of giving every segment an identical subnet size.

For the full subnet masks, usable ranges, broadcast addresses, and allocation table, see [Subnetting and VLSM](subnetting-vlsm.md).

## VLAN and gateway plan

| VLAN | Name | Network | Gateway |
|---:|---|---|---|
| 10 | Management | `10.10.0.112/29` | `10.10.0.118` |
| 20 | HR | `10.10.0.64/28` | `10.10.0.78` |
| 30 | Finance | `10.10.0.80/28` | `10.10.0.94` |
| 40 | Engineering | `10.10.0.0/27` | `10.10.0.30` |
| 50 | Sales & Marketing | `10.10.0.32/28` | `10.10.0.46` |
| 60 | IT | `10.10.0.96/28` | `10.10.0.110` |
| 70 | Servers | `10.10.0.120/29` | `10.10.0.126` |
| 80 | Guest | `10.10.0.48/28` | `10.10.0.62` |
| 99 | Network Management | `10.10.0.128/28` | `10.10.0.142` |
| 999 | Native / Unused | Reserved; no SVI address | — |

## Server plan

| Server role | Address | Notes |
|---|---|---|
| DNS | `10.10.0.121` | Internal DNS records include the NexaTech website name |
| Web | `10.10.0.122` | Internal and published website |
| App | `10.10.0.123` | Application server role; ACL design permits HTTPS from authorized department subnets where configured |
| File | `10.10.0.124` | File server role; ACL design permits FTP from authorized department subnets where configured |
| Default gateway | `10.10.0.126` | VLAN 70 SVI on CORE-SW |

## Infrastructure links

| Link | Interface/address A | Interface/address B | Network |
|---|---|---|---|
| CORE-SW ↔ EDGE-R1 | CORE-SW `Fa0/8` — `10.10.0.145/30` | EDGE-R1 `G0/0` — `10.10.0.146/30` | `10.10.0.144/30` |
| EDGE-R1 ↔ ASA inside | EDGE-R1 `G0/1` — `10.10.0.150/30` | ASA `G1/1` — `10.10.0.149/30` | `10.10.0.148/30` |
| ASA outside ↔ ISP | ASA `G1/2` — `203.0.113.6/30` | ISP `G0/0` — `203.0.113.5/30` | `203.0.113.4/30` |
| ISP ↔ INTERNET-SRV | ISP `G0/1` — `198.51.100.1/24` | INTERNET-SRV — `198.51.100.10/24` | `198.51.100.0/24` |

## Routing and NAT summary

- CORE-SW has a default route toward EDGE-R1 (`10.10.0.146`).
- EDGE-R1 routes the internal `10.10.0.0/16` space toward CORE-SW (`10.10.0.145`) and uses the ASA inside address (`10.10.0.149`) as its default next hop.
- ASA has a route for the internal `10.10.0.0/16` network via EDGE-R1 (`10.10.0.150`) and a default route toward ISP (`203.0.113.5`).
- Dynamic PAT translates internal network traffic to the ASA outside interface address for outbound access.
- Static NAT maps Web Server `10.10.0.122` to the simulated public address `203.0.113.10`.
- ISP has a static route for the public NAT block `203.0.113.8/29` via the ASA outside address (`203.0.113.6`). This block contains the published Web Server address `203.0.113.10`.

## Addressing note

`203.0.113.0/24` and `198.51.100.0/24` are documentation ranges used for examples and lab simulations. They are appropriate for this isolated Packet Tracer scenario, not for a production public service.
