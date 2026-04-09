# Inter-VLAN Routing using Router-on-a-Stick (RoS)

## Project Overview
The goal of this project is to segment a network into different VLANs (HR, Finance, IT) using a switch and a router. Router-on-a-Stick will be used to enable inter-VLAN routing, where the router handles communication between VLANs.

## Learning Objectives
1. Confgiure a signle route interface.
2. The aim was for the router to route traffic between multiple VLANd.
3. I learnt how to create VLANs, configure trunk ports and setting up subinterfaces.

## Devices, IP Addresses, and Cables
### Devices:
1. Router0	(Cisco 2811) was renamed to Router.
2. Switch0	(Cisco 2960) was renamed to	Switch.
3. PC0	(Cisco PC) was renamed to	HR.
4. PC1	(Cisco PC) was renamed to Finance.
5. PC2	(Cisco PC)	was renamed to IT.

### IP Addresses:
1.	HR PC: 192.168.10.10/24
o	Default Gateway: 192.168.10.1

2.	Finance PC: 192.168.20.10/24
o	Default Gateway: 192.168.20.1

3.	IT PC: 192.168.30.10/24
o	Default Gateway: 192.168.30.1

### Cables:
A Copper Straight-through cable was used to connect all the devices.
1. Router (FastEthernet0/0) to Switch (FastEthernet0/4)
2.	Switch (FastEthernet0/1) to HR (FastEthernet0)
3.	Switch (FastEthernet0/2) to Finance (FastEthernet0)
4.	Switch (FastEthernet0/3) to IT (FastEthernet0)

## Network Topology
Topology Version 1:
The interface between the router and the switch is shown in the image below. The red triangles indicated this. In the following steps, I showed how to fix this issue.
*see topology_version_1.png for reference.

Correction:
I fixed the error in the Router CLI.

Commands Used
Router> en 
Router# config
Router(config)# interface FastEthernet0/0
Router(config-if)# no shutdown
Router(config-if)# exit

Topology Version 2:
*see topology_version_2.png for reference.

## Implementation Steps
Step 1: Configure VLANs
I then created VLANs on the switch for HR, Finance, and IT departments.

Commands Used
Switch> en
Switch# config
Switch(config)# vlan 10
Switch(config-vlan)# name HR
Switch(config-vlan)# exit
Switch(config)# vlan 20
Switch(config-vlan)# name Finance
Switch(config-vlan)# exit
Switch(config)# vlan 30
Switch(config-vlan)# name IT
Switch(config-vlan)# exit
 
Step 2: Assign VLANs to Switch Ports
The VLANs were then assigned to the switch port.

Commands Used
Switch> en
Switch# config
Switch(config)# interface FastEthernet0/1
Switch(config-if)# switchport mode access
Switch(config-if)# switchport access vlan 10
Switch(config-if)# exit

Switch(config)# interface FastEthernet0/2
Switch(config-if)# switchport mode access
Switch(config-if)# switchport access vlan 20
Switch(config-if)# exit

Switch(config)# interface FastEthernet0/3
Switch(config-if)# switchport mode access
Switch(config-if)# switchport access vlan 30
Switch(config-if)# exit

Step 3: Router-on-a-Stick
The Subinterfaces were configured on the router. Each Subinterface was enabled on the router for each VLAN. This allows the router to route traffic between VLANs.

Commands Used
Router> en
Router# config
Router(config)# interface FastEthernet0/0.10
Router(config-if)# encapsulation dot1Q 10
Router(config-if)# ip address 192.168.10.1 255.255.255.0
Router(config-if)# exit

Router(config)# interface FastEthernet0/0.20
Router(config-if)# encapsulation dot1Q 20
Router(config-if)# ip address 192.168.20.1 255.255.255.0
Router(config-if)# exit

Router(config)# interface FastEthernet0/0.30
Router(config-if)# encapsulation dot1Q 30
Router(config-if)# ip address 192.168.30.1 255.255.255.0
Router(config-if)# exit
 
Step 4: Configure Trunking on the Switch Port
In this step, I configured the trunking between the switch and the router to allow VLAN traffic to pass between the two devices.

Commands Used
Switch> en
Switch# config
Switch(config)# interface FastEthernet0/4
Switch(config-if)# switchport mode trunk
Switch(config-if)# exit
 
Step 5: Configure DHCP Pools on the Router
DHCP pools were then created for each VLAN to dynamically assign IP addresses to PCs in each VLAN.

Commands Used
Router> en
Router# config
Router(config)# ip dhcp pool HR
Router(dhcp-config)# network 192.168.10.0 255.255.255.0
Router(dhcp-config)# default-router 192.168.10.1
Router(dhcp-config)# dns-server 8.8.8.8
Router(dhcp-config)# exit

Router(config)# ip dhcp pool Finance
Router(dhcp-config)# network 192.168.20.0 255.255.255.0
Router(dhcp-config)# default-router 192.168.20.1
Router(dhcp-config)# dns-server 8.8.8.8
Router(dhcp-config)# exit

Router(config)# ip dhcp pool IT
Router(dhcp-config)# network 192.168.30.0 255.255.255.0
Router(dhcp-config)# default-router 192.168.30.1
Router(dhcp-config)# dns-server 8.8.8.8
Router(dhcp-config)# exit

Step 8: Exclude Router’s IP from DHCP Pool
The router’s IP addresses were excluded from the DHCP pool to avoid assigning them to PCs.

Commands Used
Router> en
Router# config
Router(config)# ip dhcp excluded-address 192.168.10.1
Router(config)# ip dhcp excluded-address 192.168.20.1
Router(config)# ip dhcp excluded-address 192.168.30.1
Router(config)# exit

Step 9: Set PCs 
Configure PCs:
Each of the PCs was configured to use DHCP for the IP address assignment. Within each PC, I set the IP configuration to Obtain an IP address automatically (DHCP) in the Desktop > IP Configuration menu.

Step 10: Test DHCP and Connectivity
I then verified the IP address assignments on each PC.

Command Used
C:> ipconfig

Test Connectivity:
I then tested the connectivity between the PCs.

1.	HR PC: 192.168.10.20  
2.	Finance PC: 192.168.20.20  
3.	IT PC: 192.168.30.20  

 
HR PC:
Commands Used
C:> ping 192.168.20.20  
C:> ping 192.168.30.20  

Finance PC:
Commands Used
C:> ping 192.168.10.20  
C:> ping 192.168.30.20  

IT PC:
Commands Used
C:> ping 192.168.10.20  
C:> ping 192.168.20.20  



