### VLAN Overview 
[Follow this link](https://github.com/amroczeK/Networking/blob/master/1.0%20LAN%20Switching%20Technologies/1.1%20Configure%2C%20verify%2C%20and%20troubleshoot%20VLANs.md)


#### Normal Range VLANs
* Used in small- and medium-sized business and enterprise networks.
* Identified by a VLAN ID between 1 and 1005.
* IDs 1002 through 1005 are reserved for Token Ring and FDDI VLANs.
* IDs 1 and 1002 to 1005 are automatically created and cannot be removed.
* Configurations are stored within a VLAN database file, called vlan.dat. The vlan.dat file is located in the flash memory of the switch.
* The VLAN Trunking Protocol (VTP), which helps manage VLAN configurations between switches, can only learn and store normal range VLANs.

#### Extended Range VLANs
* Enable service providers to extend their infrastructure to a greater number of customers. Some global enterprises could be large enough to need extended range VLAN IDs.
* Are identified by a VLAN ID between 1006 and 4094.
* Configurations are not written to the vlan.dat file.
* Support fewer VLAN features than normal range VLANs.
* Are, by default, saved in the running configuration file.
* VTP does not learn extended range VLANs.


#### Creating and Deleting a VLAN
```
!!! Creating VLAN
S1(config)# vlan 20
S1(config-vlan)# name Accounting

!!! Deleting VLAN
S1(config)# no vlan 20
```
#### Assigning Ports to VLANs
```
S1(config)# interface F0/18
S1(config-if)# switchport mode access
S1(config-if)# switchport access vlan 20
```
#### Changing VLAN Port Membership
```
S1(config)# interface F0/18
S1(config-if)# no switchport acess vlan
```
#### Configuring VLAN Trunks / Trunk links
```
S1(config)# interface F0/18
S1(config-if)# switchport mode trunk
S1(config-if)# switchport trunk native vlan 10
S1(config-if)# switchport trunk allowed vlan 20,30,50
```
#### Removing VLAN Trunk Configuration on Interface
```
S1(config)# interface F0/18
S1(config-if)# no switchport trunk allowed vlan
S1(config-if)# no switchport trunk native vlan
```



#### Verifying VLAN Implementation
```
S1# show vlan
S1# show vlan brief
S1# show vlan summary
S1# show vlan name Accounting
S1# show interfaces vlan 20
S1# show interfaces F0/18 switchport
```

