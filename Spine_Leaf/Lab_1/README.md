## Lab 1 - Layer 3 Spine-Leaf with eBGP ECMP Underlay
<details>
<summary>Lab Overview</summary>
   
   - This lab is built with containerlab and Arista cEOS docker image.
   - Layer 3 Spine-Leaf network (3-stage Clos Fabric) with eBGP ECMP as underlay routing.
   - Two Spine and four Leaf switches.
   - Each Leaf switch has its own BGP AS number.
   - Two Spine switches have the same BGP AS number.
   - BGP peering (eBGP) between Spine and Leaf switches.
   - No BGP peering between Spine switches and no BGP peering between Leaf switches.

</details>

<details>
<summary>Lab Topologies</summary>

- Figure-1 - Physical Connectivity
  
![Physical Connectivity](topologies/Lab_1%20-%20Physical%20Connectivity.png)

- Figure-2 - Underlay Addressing - IPv4

![Underlay Addressing - IPv4](topologies/Lab_1%20-%20Underlay%20Addressing%20-%20IPv4.png)

- Figure-3 - Underlay Routing - eBGP

![Underlay Routing - eBGP](topologies/Lab_1%20-%20Underlay%20Routing%20-%20eBGP.png)

</details>

<details>
<summary>Lab Deployment</summary>

- Deploy the lab with topology file 'spine_leaf.clab.yml':
```
~/clab-arista$ clab deploy -t spine_leaf.clab.yml
22:30:47 INFO Containerlab started version=0.71.0
22:30:47 INFO Parsing & checking topology file=spine_leaf.clab.yml
22:30:47 INFO Creating docker network name=clab IPv4 subnet=172.20.20.0/24 IPv6 subnet=3fff:172:20:20::/64 MTU=0
22:30:48 INFO Creating lab directory path=/home/clab/clab-arista/clab-spine_leaf
22:30:48 INFO Creating container name=spine_2
22:30:48 INFO Creating container name=spine_1
22:30:48 INFO Creating container name=leaf_3
22:30:48 INFO Creating container name=leaf_4
22:30:48 INFO Creating container name=leaf_2
22:30:48 INFO Creating container name=leaf_1
22:30:50 INFO Running postdeploy actions for Arista cEOS 'leaf_4' node
22:30:50 INFO Running postdeploy actions for Arista cEOS 'leaf_3' node
22:30:50 INFO Created link: spine_2:eth1 ▪┄┄▪ leaf_1:eth2
22:30:50 INFO Running postdeploy actions for Arista cEOS 'leaf_1' node
22:30:50 INFO Created link: spine_1:eth1 ▪┄┄▪ leaf_1:eth1
22:30:50 INFO Created link: spine_2:eth2 ▪┄┄▪ leaf_2:eth2
22:30:50 INFO Running postdeploy actions for Arista cEOS 'leaf_2' node
22:30:50 INFO Created link: spine_1:eth2 ▪┄┄▪ leaf_2:eth1
22:30:50 INFO Created link: spine_2:eth3 ▪┄┄▪ leaf_3:eth2
22:30:50 INFO Created link: spine_2:eth4 ▪┄┄▪ leaf_4:eth2
22:30:50 INFO Running postdeploy actions for Arista cEOS 'spine_2' node
22:30:50 INFO Created link: spine_1:eth3 ▪┄┄▪ leaf_3:eth1
22:30:50 INFO Created link: spine_1:eth4 ▪┄┄▪ leaf_4:eth1
22:30:50 INFO Running postdeploy actions for Arista cEOS 'spine_1' node
22:32:13 INFO Adding host entries path=/etc/hosts
22:32:13 INFO Adding SSH config for nodes path=/etc/ssh/ssh_config.d/clab-spine_leaf.conf
🎉 A newer containerlab version (0.71.1) is available!
Release notes: https://containerlab.dev/rn/0.71/#0711
Run 'sudo clab version upgrade' or see https://containerlab.dev/install/ for installation options.
╭─────────┬──────────────┬─────────┬───────────────────╮
│   Name  │  Kind/Image  │  State  │   IPv4/6 Address  │
├─────────┼──────────────┼─────────┼───────────────────┤
│ leaf_1  │ ceos         │ running │ 172.20.20.6       │
│         │ ceos:4.35.0F │         │ 3fff:172:20:20::6 │
├─────────┼──────────────┼─────────┼───────────────────┤
│ leaf_2  │ ceos         │ running │ 172.20.20.7       │
│         │ ceos:4.35.0F │         │ 3fff:172:20:20::7 │
├─────────┼──────────────┼─────────┼───────────────────┤
│ leaf_3  │ ceos         │ running │ 172.20.20.5       │
│         │ ceos:4.35.0F │         │ 3fff:172:20:20::5 │
├─────────┼──────────────┼─────────┼───────────────────┤
│ leaf_4  │ ceos         │ running │ 172.20.20.2       │
│         │ ceos:4.35.0F │         │ 3fff:172:20:20::2 │
├─────────┼──────────────┼─────────┼───────────────────┤
│ spine_1 │ ceos         │ running │ 172.20.20.4       │
│         │ ceos:4.35.0F │         │ 3fff:172:20:20::4 │
├─────────┼──────────────┼─────────┼───────────────────┤
│ spine_2 │ ceos         │ running │ 172.20.20.3       │
│         │ ceos:4.35.0F │         │ 3fff:172:20:20::3 │
╰─────────┴──────────────┴─────────┴───────────────────╯
```
- Inspect the topology 'spine_leaf.clab.yml' after deployment:
```
~/clab-arista$ clab inspect -t spine_leaf.clab.yml
22:35:24 INFO Parsing & checking topology file=spine_leaf.clab.yml
╭─────────┬──────────────┬─────────┬───────────────────╮
│   Name  │  Kind/Image  │  State  │   IPv4/6 Address  │
├─────────┼──────────────┼─────────┼───────────────────┤
│ leaf_1  │ ceos         │ running │ 172.20.20.6       │
│         │ ceos:4.35.0F │         │ 3fff:172:20:20::6 │
├─────────┼──────────────┼─────────┼───────────────────┤
│ leaf_2  │ ceos         │ running │ 172.20.20.7       │
│         │ ceos:4.35.0F │         │ 3fff:172:20:20::7 │
├─────────┼──────────────┼─────────┼───────────────────┤
│ leaf_3  │ ceos         │ running │ 172.20.20.5       │
│         │ ceos:4.35.0F │         │ 3fff:172:20:20::5 │
├─────────┼──────────────┼─────────┼───────────────────┤
│ leaf_4  │ ceos         │ running │ 172.20.20.2       │
│         │ ceos:4.35.0F │         │ 3fff:172:20:20::2 │
├─────────┼──────────────┼─────────┼───────────────────┤
│ spine_1 │ ceos         │ running │ 172.20.20.4       │
│         │ ceos:4.35.0F │         │ 3fff:172:20:20::4 │
├─────────┼──────────────┼─────────┼───────────────────┤
│ spine_2 │ ceos         │ running │ 172.20.20.3       │
│         │ ceos:4.35.0F │         │ 3fff:172:20:20::3 │
╰─────────┴──────────────┴─────────┴───────────────────╯
```
- SSH access to a device using node name and username/password is `admin/admin`
```
~/clab-arista$ ssh admin@spine_1
Warning: Permanently added 'spine_1' (ED25519) to the list of known hosts.
(admin@spine_1) Password:
spine_1>
spine_1>en
spine_1#
```
- Run commands such as `show run`, `show int status` and `show lldp neighbors` to verify the connectivities.
```
spine_1#show run
! Command: show running-config
! device: spine-1 (cEOSLab, EOS-4.35.0F-44178984.4350F (engineering build))
!
no aaa root
!
username admin privilege 15 role network-admin secret sha512 $6$u96wqhebo3oaICHs$zWFoEcb4IbkHR2tgb5U3b9JpghxtNdErJEZzSqbcoB42OPYVPsa0g7KjgPcOWZpkd/K1cUyXbyHI6roEy5Wtt1
!
management api http-commands
   no shutdown
!
no service interface inactive port-id allocation disabled
!
transceiver qsfp default-mode 4x10G
!
service routing protocols model multi-agent
!
hostname spine_1
!
spanning-tree mode mstp
!
system l1
   unsupported speed action error
   unsupported error-correction action error
!
management api gnmi
   transport grpc default
!
management api netconf
   transport ssh default
!
interface Ethernet1
!
interface Ethernet2
!
interface Ethernet3
!
interface Ethernet4
!
interface Management0
   ip address 172.20.20.4/24
   ipv6 address 3fff:172:20:20::4/64
!
no ip routing
!
ip route 0.0.0.0/0 172.20.20.1
!
ipv6 route ::/0 3fff:172:20:20::1
!
router multicast
   ipv4
      software-forwarding kernel
   !
   ipv6
      software-forwarding kernel
!
end
pine_1#show int status
Port       Name   Status       Vlan     Duplex Speed  Type            Flags Encapsulation
Et1               connected    1        full   1G     EbraTestPhyPort
Et2               connected    1        full   1G     EbraTestPhyPort
Et3               connected    1        full   1G     EbraTestPhyPort
Et4               connected    1        full   1G     EbraTestPhyPort
Ma0               connected    routed   a-full a-1G   10/100/1000

spine_1#show lldp ne
Last table change time   : 0:04:04 ago
Number of table inserts  : 9
Number of table deletes  : 0
Number of table drops    : 0
Number of table age-outs : 0

Port Neighbor Device ID Neighbor Port ID TTL
---- ------------------ ---------------- ---
Et1  leaf_1             Ethernet1        120
Et2  leaf_2             Ethernet1        120
Et3  leaf_3             Ethernet1        120
Et4  leaf_4             Ethernet1        120
Ma0  leaf_4             Management0      120
Ma0  leaf_1             Management0      120
Ma0  spine_2            Management0      120
Ma0  leaf_2             Management0      120
Ma0  leaf_3             Management0      120

spine_1#
```
</details>

<details>
<summary>Lab Configuration</summary>

   - The working configuration for all Spine and Leaf devices is placed in "configs" folder, [click here](configs) to go there. 

</details>
