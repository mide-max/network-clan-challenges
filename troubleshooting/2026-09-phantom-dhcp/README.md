# Troubleshooting Challenge: The Phantom DHCP Server (Beginner)

## Scenario
Clients across VLANs 10/20/30/40 can't get an IP from the centralized
DHCP server. Server is up and pingable from the core, and the access
layer is verified. Scope: CoreSwitch only.

## Symptom
All PCs stuck on 169.254.x.x (APIPA).

## Approach
1. Ruled out: server down, access ports/VLANs, routing (per brief)
2. Hypothesis: Discover broadcasts aren't reaching the server
3. Checked: `show running-config | section interface Vlan`
   -> no `ip helper-address` on any SVI

## Root Cause
DHCP Discover is a broadcast and doesn't cross Layer 3 boundaries.
The server is on a different subnet, and CoreSwitch had no relay
configured to forward the requests to it.

## Fix
interface vlan 10
 ip helper-address <DHCP-SERVER-IP>
(repeated for vlan 20, 30, 40)

## Verification
<add screenshots + show run output here>
