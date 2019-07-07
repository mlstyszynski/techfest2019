## EVPN-VXLAN - EM 5.1 fabric management hands-on lab


### Lab topology
![Lab topology-1](topologies/em_lab_main_topo.png)

The following diagram gives an overview of the flows between the EM main dashboard (server6) and the config API (server6 )

![Lab topology-1](topologies/em_lab_baseline_flow.png)
### Lab access ###

ravello URL
ravello login info:
username: userX (for example user1, user2 etc given at your desk)
password: Juniper


You can ssh using putty or secure-crt with the dns names or IP@ of the routing-engines RE listed in the table

the lab vqfx device access info: 
`root` and `Juniper1!`

the contrail EM dashboard login/password is:
`admin` and `contrail123`

the servers ssh access login/password is:
`root` and `c0ntrail123`

### Lab objectives

The goal of the present lab is to build the overlay **Edge Routed Bridging** fabric architecture using the Juniper QFX series EVPN-VXLAN technologies in order to deliver L2 active/active forwarding within the datacenter between the BMS servers and VMs. 

The ZTP process was already conducted for the present fabric so the first step is to onboard the devices from the topology and create an overlay iBGP fabric.

The iBGP EVPN sign. overlay will be used in order to advertise the MAC@ and MAC+IP in DC-1 and EVPN-type5 between the DC1 and DC2. 
 
The inter-vni tenant routing will be taking place at the leaf1/leaf2/leaf3 level only i.e. spines should not be provisioned with any tenant information but should play the role of Route Reflectors and IP Forwarders between the leafs

Spine3-re is deployed in DC-2 is connected to the same overlay ASN 64512 and  Spine1-re/Spine2-re but DC-1 to DC-2 exchanges only EVPN type-5 route for prefix-advertisement. 

The ultimate goal of the present lab is to deliver:
 - L2 communication between BMS1 and BMS2 - they should reside in the same virtual-network with VLAN-VNIs allocated dynamically by contrail
 - L3 inter-vni communication should be symmetric and take place only at the leafs 
 - L2 VXLAN gateway for BMS1 to VM2 (on compute-server-3) should allow to communicate between the BMS1 and the VM2 using the same subnet 

The L1-task20 is introducing the **Edge Routed Bridging Overlay** architecture using the IP anycast IRB approach and the inter-POD EVPN route T5. 
### Lab environment

The  environment is composed of the following vqfx nodes: 
- 2 x vQFX Spines - spine1, spine2
- 3 x vQFX Leafs: leaf1, leaf2, leaf3 
- 3 x BMS servers - bm1, bms2, bms3, bms4
- 2 x compute-servers - server3 and server4
- 1 x CSN server -  server5
- 1 x controller - server1
- 1 x EM dashboard - server6

The underlay eBGP is already pre-provisioned in order to deliver full IP reachability between the loopback0.0 IP@ of the given IP fabric 

> All nodes from the lab are accessible via internet, so you can verify each individual node state from your laptop using SSH sessions 

Use the username: `root` and password: `Juniper1!`


### Lab tasks


> **Task 1.0 verify the full IPv4 underlay reachability within the**  [main topology](https://github.com/juniper-dc/techfest2019#lab-topology)


+ Ensure the full IP reachability of your fabric
+ Verify the eBGP underlay peering are all in the established state

*Expected result*
+ two eBGP peerings per leaf device in the Established state, 
+ Full IP reachability between the lo0.0 IP addresses of each fabric node

---

> **task 1.1 deploy the overlay iBGP EVPN network using the BGP ASN 64512**

+ Discover the devices as based on the given range of subnet 10.0.0.0/24 
+ Associate the devices with the roles 
+ Deploy the overlay

*Expected result*
+ The leaf and spine devices are discovered and each device is associated with the Edge-Routed architecture roles 
+ Spines1/2 should have role: null (aka Lean Spine) and Route-Reflector 
+ Leafs1/2/3 should have the role: ERB-UCAST-Gateway
---

### Implementation details: 

The fabric discovery network: `10.0.0.0/24` 

Overlay iBGP ASN number: `64512`

BMS1 and BMS2 virtual network IP@ range: `100.0.201.30-100.0.201.45` subnet mask `/24`

BMS3 virtual network IP@ range: `100.0.203.30-100.0.203.45` subnet mask `/24`

BMS4 virtual network IP@ range:  `100.0.204.30-100.0.204.45` subnet mask `/24`



### Solution guide for EVPN/VXLAN lab ####

