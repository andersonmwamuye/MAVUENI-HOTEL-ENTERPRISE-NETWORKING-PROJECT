**########### CONFIGS STEPS ############**



1.Basic settings to all devices plus SSH on the routers and 13 switches

2.VLANs assignment plus all access and trunk ports on 12 and 13 switches.

3.Switchort security to finance department.

4.Subnetting and IP addressing

5.OSPF on the routers and 13 switches.

6.Static IP address to server Room devices.

7.DHCP server device configurations.

8.Inter-VLAN routing on the 13 switches plus IP DHCP helper addresses

9.Wireless network configurations.

10.PAT + Access Control List

10.1.1 Default Static Route

11\. Verifying and testing Configurations.





**STEP 1**



**...........Access switches..........**

en

config t



hostname Server-SW

line console 0

password makupe

login

exit



enable password makupe

no ip domain-lookup

banner motd #NO Unauthorised Access!!!#

service password-encryption



do wr



**Multilayer switches \& CORE-Routers**

&#x20;  **-Both basic configs and SSH configs**

**the SSH configurations are done on layer 3 switches only!!!!**

en

config t



hostname CORE-R2

line console 0

password makupe

login

exit



enable password makupe

no ip domain-lookup

banner motd #NO Unauthorised Access!!!#

service password-encryption



do wr

&#x20;                           **.......Layer 3 switches.........**

ip domain name makupe.net

username admin password makupe

crypto key generate rsa

1024

line vty 0 15

login local

transport input ssh

exit

ip ssh version 2

do wr



**STEP 2 \& STEP 3**

**....trunking is done on the ports connected between the access switches and layer 3 switches.....**

config t

int range fa0/1-2

switchport mode trunk

exit



vlan 60

name ServerRoom

vlan 99

name BlackHole

exit





int range fa0/3-24

switchport mode access

switchport access vlan 60

exit



int range gig0/1-2

switchport mode access

switchport access vlan 99

shutdown

exit



do wr



**Switchport security-Finance switch**



config t

int range fa0/3-24

switchport port-security

switchport port-security maximum 1

switchport port-security mac-address sticky

switchport port-security violation shutdown

exit

do wr



**Layer-3 switches**

config t

int range gig1/0/3-8

switchport mode trunk

exit



vlan 10

name Sales

vlan 20

name HR

vlan 30

name Finance

vlan 40

name Admin

vlan 50

name ICT

vlan 60

name ServerRoom

exit

do wr





**STEP 4**

**ip-addressing**

**Base Network: 172.16.1.0**



&#x20;                                 **First Floor**

|**Department**|**Network Address**|**Subnet Mask**|**Host Address Range**|**Broadcast Address**|
|-|-|-|-|-|
|Sales 7 Marketing|172.16.1.0|255.255.255.128/25|172.16.1.1 to 172.16.1.126|172.16.1.127|
|HR \& Logistics|172.16.1.128|255.255.255.128/25|172.16.1.129 to 172.16.1.254|172.16.1.255|



&#x20;                                 **Second Floor**

|**Department**|**Network Address**|**Subnet Mask**|**Host Address Range**|**Broadcast Address**|
|-|-|-|-|-|
|Finance \& Accounts|172.16.2.0|255.255.255.128/25|172.16.2.1 to 172.16.2.126|172.16.2.127|
|Admin \& Public Relations|172.16.2.128|255.255.255.128/25|172.16.2.129 to 172.16.2.254|172.16.2.255|







&#x20;                                 **Third Floor**

|**Department**|**Network Address**|**Subnet Mask**|**Host Address Range**|**Broadcast Address**|
|-|-|-|-|-|
|ICT|172.16.3.0|255.255.255.128/25|172.16.3.1 to 172.16.3.126|172.16.3.127|
|Server Room|172.16.3.128|255.255.255.240/28|172.16.3.129 to 172.16.3.142|172.16.3.143|





&#x20;              **Between the Routers and Layer-3 Switches**

|**No.**|**Network Address**|**Subnet Mask**|**Host Address Range**|**Broadcast Address**|
|-|-|-|-|-|
|R1-MLSW1|172.16.3.144|255.255.255.252|172.16.3.145 to 172.16.3.146|172.16.3.147|
|R1-MLSW2|172.16.3.148|255.255.255.252|172.16.3.149 to 172.16.3.150|172.16.3.151|
|R2-MLSW1|172.16.3.152|255.255.255.252|172.16.3.153 to 172.16.3.154|172.16.3.155|
|R2-MLSW2|172.16.3.156|255.255.255.252|172.16.3.157 to 172.16.3.158|172.16.3.159|





**Between the Routers and ISPs**



Public IP addresses 195.136.17.0/30, 195.136.7.4/30, 195.136.17.8/30 and 195.136.17.12/30



**Layer-3 switches Configs**

**......ip addressing on the layer 3 switches and all routers. The ISPs routers we don't have power over them but I have configured ip addresses in the interfaces for demonstration purposes.....**



int range gig1/0/1-2

no switchport

exit

do wr



**STEP 5**

**....OSPF configs in all the switches and routers....**



**Layer-3 Switches**



ip routing

router ospf 10

router-id 1.1.1.1

network 172.16.1.0 0.0.0.127 area 0

network 172.16.1.128 0.0.0.127 area 0

network 172.16.2.0 0.0.0.127 area 0

network 172.16.2.128 0.0.0.127 area 0

network 172.16.3.0 0.0.0.127 area 0

network 172.16.3.128 0.0.0.15 area 0

network 172.16.3.152 0.0.0.3 area 0

network 172.16.3.144 0.0.0.3 area 0

exit

do wr



**Routers**

router ospf 10

router-id 6.6.6.6

network 195.136.17.12 0.0.0.3 area 0

network 195.136.17.4 0.0.0.3 area 0

ex

do wr



**STEP 6**

**.........Static ip addressing in the server room........**

Email server address-

DHCP-Server  address-172.16.3.130

DNS-Server   address-172.16.3.131

SysAdmin-PC  address-172.16.3.132

Default gateway     -172.16.3.129





**STEP 8**

**..inter-VLAN routing \& DHCP helper address...in layer 3 switches**



int vlan 10

no shutdown

ip add 172.16.1.1 255.255.255.128

ip helper-address 172.16.3.130

exit



int vlan 20

no shutdown

ip add 172.16.1.129 255.255.255.128

ip helper-address 172.16.3.130

exit



int vlan 30

no shutdown

ip add 172.16.2.1 255.255.255.128

ip helper-address 172.16.3.130

exit



int vlan 40

no shutdown

ip add 172.16.2.129 255.255.255.128

ip helper-address 172.16.3.130

exit



int vlan 50

no shutdown

ip add 172.16.3.1 255.255.255.128

ip helper-address 172.16.3.130

exit



int vlan 60

no shutdown

ip add 172.16.3.129 255.255.255.240

ip helper-address 172.16.3.130

exit

do wr





**STEP 9**

**...Wireless network configs...**





**STEP 10**



**....PAT(port address translation) \& ACCESS CONTROL LIST....done on core routers**



ip nat inside source list 1 int se0/2/0 overload

ip nat inside source list 1 int se0/2/1 overload

access-list 1 permit 172.16.1.0 0.0.0.127

access-list 1 permit 172.16.1.128 0.0.0.127

access-list 1 permit 172.16.2.0 0.0.0.127

access-list 1 permit 172.16.2.128 0.0.0.127

access-list 1 permit 172.16.3.0 0.0.0.127

access-list 1 permit 172.16.3.128 0.0.0.15

int range gig0/0-1

ip nat inside

exit



int se0/2/0

ip nat outside

int se0/2/1

ip nat outside

ex

do wr





**STEP 10.1.1**

**.....DEFAULT STATIC ROUTING......**



**Layer 3 switches**

ip route 0.0.0.0 0.0.0.0 gig1/0/1

ip route 0.0.0.0 0.0.0.0 gig1/0/2 70

do wr



**Core-routers**

ip route 0.0.0.0 0.0.0.0 se0/2/0

ip route 0.0.0.0 0.0.0.0 se0/2/1 70

do wr

