# Testing Results

The table summarizes validation scenarios performed in Cisco Packet Tracer 9.0.1. The results describe this lab configuration and do not imply production-level assurance.

| ID | Test | Expected result | Observed result | Notes |
|---|---|---|---|---|
| T01 | DHCP and IP addressing across client VLANs | PCs in all configured client VLANs receive the correct IP address, subnet mask, default gateway, and DNS settings | PASS | Verified across the client VLANs. Servers (VLAN 70) and Network Management (VLAN 99) are excluded from this DHCP test and use their assigned addressing configuration. |
| T02 | PC to its default gateway | The assigned gateway responds to ICMP echo requests | PASS | First packet loss may occur during ARP learning; check sustained behavior. |
| T03 | Internal DNS and website | `http://www.nexatech.local` opens | PASS | Site also opened directly using `http://10.10.0.122`. |
| T04 | Guest-to-HR ICMP test | Ping from Guest-PC to a tested HR host is blocked | PASS | Verified using a ping test from Guest-PC to an HR host. |
| T05 | Guest to Web Server | HTTP access to `10.10.0.122` is allowed | PASS | Confirm using the web browser, not only ping. |
| T06 | SSH from the designated administration PC | SSH to CORE management address succeeds for authorized host | PASS | Verified successful SSH authentication using the configured username and password. |
| T07 | Outbound access through PAT | Internal PC opens `http://198.51.100.10` | PASS | Internet Server's default Packet Tracer page loaded. |
| T08 | Public HTTP to Web Server | `http://203.0.113.10` opens from INTERNET-SRV | PASS | The ASA's static NAT and HTTP path were tested. |
| T09 | Public HTTPS to Web Server | `https://203.0.113.10` opens from INTERNET-SRV | PASS | HTTPS service on WEB-SRV was enabled. |
| T10 | FTP attempt to public Web address | Unpublished FTP is blocked | PASS (blocked) | The outside policy is designed to publish HTTP/HTTPS only. |

## Testing Notes

- Successful HTTP/HTTPS tests confirm connectivity for those application protocols; they do not imply that ICMP traffic is permitted.