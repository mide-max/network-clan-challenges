# October Design Challenge: TechPoint Solutions (Beginner Track)

IT Village Network Clan | October 2026 | Tool: Cisco Packet Tracer

## Scenario
TechPoint Solutions is a 15-person, two-floor office running a flat network behind a basic ISP router. The challenge was to redesign it to be secure, reliable, manageable and ready to grow to about 30 staff.

## Problems and solutions

| # | Problem | Solution |
|---|---|---|
| 1 | Network slows when cameras record | Cameras on their own VLAN (30) so video traffic is separate from staff traffic |
| 2 | Guests share the employee Wi-Fi password | Separate Guest SSID mapped to Guest VLAN (20), internet-only |
| 3 | NAS reachable from every device | NAS in its own Servers VLAN (50); only Employees allowed |
| 4 | Cameras not isolated | VLAN 30; cameras never initiate connections, the NAS pulls the streams |
| 5 | No management strategy | Management VLAN (40), SSH only from VLAN 40, unused ports shut down |
| 6 | Single ISP link | Dual WAN with a floating static route as failover |
| 7 | Growth from 15 to 30 staff | Right-sized subnets, spare ports, second switch planned |

## Topology
![Topology](screenshots/01-topology.png)

## Devices
2x Router 2911 (TP-FW1, ISP), 1x Switch 2960-24TT (TP-SW1), 2x Access Point-PT, 8 employee PCs, 7 staff laptops + 1 guest laptop, 2 printers, 4 cameras, 1 NAS, 1 admin PC, 1 internet server.

## VLAN and IP plan

| VLAN | Name | Subnet | Gateway | Notes |
|---|---|---|---|---|
| 10 | EMPLOYEES | 10.10.10.0/24 | 10.10.10.1 | DHCP .50 to .250 |
| 20 | GUEST | 10.10.20.0/24 | 10.10.20.1 | DHCP .10 to .250 |
| 30 | CAMERAS | 10.10.30.0/26 | 10.10.30.1 | Static .11 to .14 (10 cameras in the twist) |
| 40 | MGMT | 10.10.40.0/27 | 10.10.40.1 | Static only |
| 50 | SERVERS | 10.10.50.0/27 | 10.10.50.1 | NAS 10.10.50.10 |
| 60 | PRINTERS | 10.10.60.0/27 | 10.10.60.1 | Printers .11 and .12 |
| 99 | UNUSED-NATIVE | none | none | Native VLAN and unused ports |

## Design summary
- **TP-FW1** is the gateway for every VLAN (router-on-a-stick over one 802.1Q trunk), runs DHCP, NAT/PAT and the ACL-based firewall policy.
- **TP-SW1** is the managed switch with VLAN access ports and the trunk to the router.
- **Security policy:** guests reach only the internet; cameras never initiate connections; only employees reach the NAS; management only from VLAN 40; everything else denied.
- **Failover:** primary default route via WAN1, floating static route (AD 250) via WAN2.

## Verification
- [x] DHCP leases addresses to the VLANs
- [x] Failover: default route moves to WAN2 when WAN1 is shut down, and returns when it is restored
- [ ] Guest isolation
- [ ] Employees reach the NAS
- [ ] Cameras cannot initiate connections
- [ ] SSH works only from the management VLAN

Screenshots are in [`screenshots/`](screenshots/). Device configurations are in [`configs/`](configs/).

## Packet Tracer limitations
- PoE is not simulated.
- Cameras and printers are PCs.
- One AP per SSID, instead of one AP with tagged SSIDs.
- Route-map NAT is not supported, so NAT was configured on both WAN interfaces and the NAT interface was switched manually to test failover.
- HTTP stands in for SMB toward the NAS.

## The twist: 10 cameras
The camera VLAN does not change (a /26 holds 62 hosts). One 24-port switch runs out of ports at 10 cameras, so a second PoE switch is added and the PoE budget is checked.

## Files
- `lab/` Packet Tracer file
- `configs/` router and switch configs
- `docs/` completed workbook
