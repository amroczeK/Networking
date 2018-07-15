#### Cisco switch boot sequence:
1. First, the switch loads a power-on self-test (POST) program stored in ROM. POST checks the CPU subsystem. It tests the CPU, DRAM, and the portion of the flash device that makes up the flash file system.
2. Next, the switch loads the boot loader software. The boot loader is a small program stored in ROM and is run immediately after POST successfully completes.
3. The boot loader performs low-level CPU initialization. It initializes the CPU registers, which control where physical memory is mapped, the quantity of memory, and its speed.
4. The boot loader initializes the flash file system on the system board.
5. Finally, the boot loader locates and loads a default IOS operating system software image into memory and hands control of the switch over to the IOS.


### Basic Switch Configuration
```
!!! Configure switch hostname
S1(config)# hostname S1

!!! Configure password encryption
S1(config)# service password-encryption

!!! Configure secret password for privileged EXEC mode access
S1(config)# enable secert password123

!!! Prevent unwanted DNS lookups
S1(config)# no ip domain-lookup

!!! Configure a MOTD banner
S1(config)# bannter motd # Unauthorized access is strictly prohibited. #

!!! Configure the IP default gateway for S1 e.g. IP of the router
S1(config)# ip default-gateway 192.168.1.1

!!! Secure console port access & prevent console messages from interrupting commands using logging synchronous
S1(config)# line con 0
S1(config-line)# password password123
S1(config-line)# login
S1(config-line)# logging synchronous

!!! Secure virtual terminal lines for the swtich to allow Telnet access
S1(config)# line vty 0 15
S1(config-line)# password password123
S1(config-line)# login

!!! Setup static MAC address on switch port. This specifies what ports a host can connect to e.g. PC-A = 0050.56BE.6C89
S1(config)# mac address-table static 0050.56BE.6C89 vlan 99 interface fastethernet 0/6

NOTE: To remove do: "no mac address-table static 0050.56BE.6C89 vlan 99 interface fastethernet 0/6"
```

#### Configure SVI IP address of the switch to allow remote management of the switch from PC-A.
```
!!! Best practice: Change management VLAN to a VLAN other than VLAN 1 then set the IP address of the switch to 192.168.1.2/24 on internal virtual interface VLAN 99.

S1(config)# vlan 99
S1(config-vlan)# description MGMT
S1(config-vlan)# exit
S1(config)# interface vlan99
S1(config-if)# ip address 192.168.1.2 255.255.255.0
S1(config-if)# no shutdown

!!! Assign all user ports to VLAN 99 so VLAN 1 is unsued and changes state to down

S1(config)# interface range f0/1 - 24, g0/1 -2
S1(config-if)# switchport mode access
S1(config-if)# switchport access vlan 99
```

#### Configure Duplex and Speed
```
S1(config)# interface f0/1
S1(config-if)# duplex full
S1(config-if)# speed 100
```

#### Configure SSH for Remote Management
```
S1(config)# ip domain-name cisco.com
S1(config)# crypto key generate rsa 1024
S1(config)# username admin secret password123
S1(config-line)# line vty 0 15
S1(config-line)# transport input ssh
S1(config-line)# login local
S1(config-line)# exit
S1(config)# ip ssh version 2

!!! Verify SSH
S1# show ip ssh
S1# show ssh
```

#### Configure DHCP Snooping to setup trusted port connections to DHCP server to prevent DHCP spoofing/evil DHCP servers
```
S1(config)# ip dhcp snooping                  !!! Enable DHCP snooping
S1(config)# ip dhcp snooping vlan 10,20       !!! Enables DHCP snooping for specific VLANs
S1(config)# interface f0/1
S1(config-if)# ip dhcp snooping trust         !!! Assign f0/1 as trusted port connecting to DHCP Server
S1(config)# exit
S1(config)# interface f0/2
S1(config-if)# ip dhcp snooping limit rate 5  !!! Limit rate an atacker can send bogus DHCP requests through untrusted ports to the DHCP Server
```

### Port Security
All switch ports (interfaces) should be secured before the switch is deployed for production use. One way to secure ports is by implementing a feature called port security. Port security limits the number of valid MAC addresses allowed on a port. The MAC addresses of legitimate devices are allowed access, while other MAC addresses are denied.

#### Secure MAC Address Types
There are a number of ways to configure port security. The type of secure address is based on the configuration and includes:

Static secure MAC addresses:
* MAC addresses that are manually configured on a port by using the switchport port-security mac-address mac-address interface configuration mode command. MAC addresses configured in this way are stored in the address table and are added to the running configuration on the switch.
Dynamic secure MAC addresses: 
* MAC addresses that are dynamically learned and stored only in the address table. MAC addresses configured in this way are removed when the switch restarts.
Sticky secure MAC addresses:
* MAC addresses that can be dynamically learned or manually configured, then stored in the address table and added to the running configuration.

Security Violation Modes:
* Protect: No traffic is forwarded, no syslog message sent, no error message displayed, no increase of violation counter, no port shutdown.
* Restrict: No traffic is forwarded, a syslog message is sent, no error message displayed, increase of violation counter, no port shutdown.
* Shutdown: No traffic is forwarded, no syslog message sent, no error message displayed, increase of violation counter, port is shutdown.

#### Configure Sticky Port Security
```
S1(config)# interface f0/19
S1(config-if)# switchport mode access
S1(config-if)# switchport port-security                       !!! Enables port security on interface
S1(config-if)# switchport port-security maximum 50            !!! Set maximum no. secure addresses allowed on port
S1(config-if)# switchport port-security mac-address sticky    !!! Enable sticky learning e.g. static or dynamic
S1(config-if)# switchport port-security violation shutdown    !!! mode: { protect | restrict | shutdown }

!!! Verify Port Security
S1# show port-security interface f0/19
S1# show run | begin f0/19                !!! Verify sticky MAC running-config
S1# show port-security addresss           !!! Verify Secure MAC Addresses
```

#### Configure NTP on Routers
```
R1 (10.1.1.1) = NTP Server ----- S1 ----- R2 (10.1.1.12) = NTP Client

R1(config)# ntp master 1
R2(config)# ntp server 10.1.1.1
```

#### Directory of flash:
```
S1# dir flash:
S1# show flash
```

#### Save the running config to the startup config:
```
S1# copy running-config startup-config
```

#### Verify status of physical and virutal interfaces:
```
S1# show ip interface brief
```

#### Determine MAC addresses the switch has learned:
```
S1# show mac address-table

S1# show mac address-table dynamic

S1# clear mac address-table dynamic
```

#### Remove startup configuration from NVRAM:
```
S1# erase startup-config
```

#### Delete VLAN database:
```
S1# delete vlan.dat
```

