# Security Controls

## Design goals

1. Separate departments and guest devices into dedicated VLANs.
2. Permit required business services to servers while restricting other server-subnet traffic.
3. Limit network-device management to the designated administration host.
4. Control access from the Internet to the published web service.
5. Keep implementation claims aligned with what Packet Tracer actually simulates.

## Internal ACLs

Department ACLs permit specified services to internal servers: DNS (UDP/53) to `10.10.0.121`, HTTP/HTTPS to the Web Server (`10.10.0.122`), HTTPS to the App Server (`10.10.0.123`), and FTP to the File Server (`10.10.0.124`), as configured in each policy.

The ACLs then deny other traffic from the relevant department subnets to the Server VLAN (`10.10.0.120/29`). A final `permit ip any any` allows traffic to destinations not matched by earlier rules. Therefore, these ACLs restrict server access but do not provide complete isolation between departmental VLANs.

### Guest policy

`GUEST-POLICY` is intended to:

- Permit HTTP and HTTPS from Guest (`10.10.0.48/28`) to the Web Server (`10.10.0.122`) before the broader Server VLAN deny.
- Deny remaining Guest traffic to the Server VLAN (`10.10.0.120/29`) and deny traffic to the listed internal department and management subnets.
- Permit traffic not matched by preceding rules.

Therefore, the documented guarantee is limited to the listed destination subnets and rules; it is not a universal block of all private or internal networks.

### Network-device management

`SSH_MANAGEMENT` is a standard source ACL allowing the designated administration PC (`10.10.0.129`). Its effective use depends on applying it to the device's VTY lines with the relevant SSH/login configuration. This is distinct from applying an extended traffic ACL to an SVI.

## ASA perimeter policy

- Dynamic PAT uses the ASA outside interface for outbound access from the internal `10.10.0.0/16` network.
- Static NAT publishes Web Server `10.10.0.122` as `203.0.113.10` within the lab.
- The outside ACL permits TCP/80 (HTTP) and TCP/443 (HTTPS) to the Web Server. It does not intentionally publish FTP or general internal access.

## IDS/IPS Scope

IDS/IPS placement is considered as a future security enhancement. A functioning, production-grade IDS/IPS deployment is not implemented in this project.

## Simulation Scope

This project is an educational network simulation built and tested in Cisco Packet Tracer. The results demonstrate the tested behavior within the simulator and do not constitute a production security certification.