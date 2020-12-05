# Lab-DHCPv4  
## Part 1: Part 1: Build the Network and Configure Basic Device Settings  
### Step 1: Establish an addressing scheme  
![alt-текст](https://github.com/DanilChery/CiscoProjectsDHCPv4/blob/main/lab-dhcp-schema.jpg "Текст заголовка логотипа 1")   
#### a.	One subnet, “Subnet A”, supporting 58 hosts (the client VLAN at R1).  
Subnet A: 192.168.1.0/26  
#### b.	One subnet, “Subnet B”, supporting 28 hosts (the management VLAN at R1).   
Subnet B: 192.168.1.64/27  
##### c.	One subnet, “Subnet C”, supporting 12 hosts (the client network at R2).  
Subnet C: 192.168.1.96/28  
### Step 2: Configure basic settings for each router  
#### a.	Assign a device name to the router.  
Router(config)#hostname R1  
#### b.	Disable DNS lookup to prevent the router from attempting to translate incorrectly entered commands as though they were host names  
no ip domain lookup  
#### c.	Assign class as the privileged EXEC encrypted password.  
#### d.	Assign cisco as the console password and enable login.  
#### e.	Assign cisco as the VTY password and enable login.  
line con 0  
 password 7 01100F175804  
line aux 0  
line vty 0 4  
 password 7 121A0C041104  
 login   
 transport input ssh  
line vty 5 15  
 password 7 121A0C041104  
 login  
 transport input ssh  
!  
no scheduler allocate  
!  
end  
#### f.	Encrypt the plaintext passwords.  
#### g.	Create a banner that warns anyone accessing the device that unauthorized access is prohibited.  
banner motd ^C  
anyone accessing the device that unauthorized access is prohibited  
^C  
#### h.	Save the running configuration to the startup configuration file.  
R2#wr mem  
Building configuration...  
[OK]  
R2#  
*Nov 21 11:01:16.558: %GRUB-5-CONFIG_WRITING: GRUB configuration is being updated on disk. Please wait...  
*Nov 21 11:01:17.433: %GRUB-5-CONFIG_WRITTEN: GRUB configuration was written to disk successfully.  
### Step 3: Configure Inter-VLAN Routing on R1  
#### a.	Activate interface G0/0/1 on the router  
R1(config)#int gi 0/1  
R1(config-if)#no sh  
R1(config-if)#no shutdown  
*Nov 21 09:57:48.713: %LINK-3-UPDOWN: Interface GigabitEthernet0/1, changed state to up  
*Nov 21 09:57:49.712: %LINEPROTO-5-UPDOWN: Line protocol on Interface GigabitEthernet0/1, changed state to up

![alt-текст](https://github.com/DanilChery/CiscoProjectsDHCPv4/blob/main/r1-g1.png "Текст заголовка логотипа 1")  
#### b.	Configure sub-interfaces for each VLAN as required by the IP addressing table. All sub-interfaces use 802.1Q encapsulation and are assigned the first usable address from the IP address pool you have calculated. Ensure the sub-interface for the native VLAN does not have an IP address assigned. Include a description for each sub-interface  
R1(config)#int gi 0/1.100  
R1(config-subif)#enc  
R1(config-subif)#encapsulation d  
R1(config-subif)#encapsulation dot1Q 100  
R1(config-subif)#ip ad  
R1(config-subif)#ip add  
R1(config-subif)#ip address 192.168.1.1 255.255.255.192  
#### c.	Verify the sub-interfaces are operational.  
VPCS> ip 192.168.1.2/26 192.168.1.1  
Checking for duplicate address...  
PC1 : 192.168.1.2 255.255.255.192 gateway 192.168.1.1    
  
VPCS> ping 192.168.1.1  
  
84 bytes from 192.168.1.1 icmp_seq=1 ttl=255 time=3.617 ms  
84 bytes from 192.168.1.1 icmp_seq=2 ttl=255 time=4.443 ms  
84 bytes from 192.168.1.1 icmp_seq=3 ttl=255 time=5.894 ms  
84 bytes from 192.168.1.1 icmp_seq=4 ttl=255 time=3.784 ms  
84 bytes from 192.168.1.1 icmp_seq=5 ttl=255 time=5.725 ms  
d.	  
VPCS>  
### Step 4: Configure G0/0/1 on R2, then G0/0/0 and static routing for both routers  
#### a.	Configure G0/0/1 on R2 with the first IP address of Subnet C you calculated earlier  
R2(config)#int gi 0/1  
R2(config-if)#ip ad  
R2(config-if)#ip add  
R2(config-if)#ip address 192.168.1.97 255.255.255.240  
R2(config-if)#no shu  
R2(config-if)#no shutdown  
R2(config-if)#  
*Nov 21 10:05:02.651: %LINK-3-UPDOWN: Interface GigabitEthernet0/1, changed state to up  
*Nov 21 10:05:03.651: %LINEPROTO-5-UPDOWN: Line protocol on Interface GigabitEthernet0/1, changed state to up  
#### b.	Configure interface G0/0/0 for each router based on the IP Addressing table above.  
R2(config-if)#int gi 0/0  
R2(config-if)#ip ad  
R2(config-if)#ip add  
R2(config-if)#ip address 10.0.0.2 255.255.255.252  
R2(config-if)#no shu  
R2(config-if)#no shutdown  
R2(config-if)#  
*Nov 21 10:06:52.436: %LINK-3-UPDOWN: Interface GigabitEthernet0/0, changed state to up  
*Nov 21 10:06:53.436: %LINEPROTO-5-UPDOWN: Line protocol on Interface GigabitEthernet0/0, changed state to up  
![alt-текст](https://github.com/DanilChery/CiscoProjectsDHCPv4/blob/main/r2-g0.png "Текст заголовка логотипа 1")  
#### c.	Configure a default route on each router pointed to the IP address of G0/0/0 on the other router.  
R2(config)#ip default-gateway 10.0.0.1  
#### d.	Verify static routing is working by pinging R2’s G0/0/1 address from R1.  
R2(config)#do ping 10.0.0.1  
Type escape sequence to abort.  
Sending 5, 100-byte ICMP Echos to 10.0.0.1, timeout is 2 seconds:  
.!!!!  
Success rate is 80 percent (4/5), round-trip min/avg/max = 2/2/3 ms

![alt-текст](https://github.com/DanilChery/CiscoProjectsDHCPv4/blob/main/r2-ping.png "Текст заголовка логотипа 1")  
#### e.	Save the running configuration to the startup configuration file.  
R2#wr memory  
Building configuration...  
[OK]  
R2#  
*Nov 21 10:08:54.661: %GRUB-5-CONFIG_WRITING: GRUB configuration is being updated on disk. Please wait...  
*Nov 21 10:08:55.713: %GRUB-5-CONFIG_WRITTEN: GRUB configuration was written to disk successfully.  
### Step 5: Configure basic settings for each switch.  
#### a.	Assign a device name to the switch.  
Switch(config)#hostname S1  
S1(config)#  
#### b.	Disable DNS lookup to prevent the router from attempting to translate incorrectly entered commands as though they were host names.  
S1(config)#no ip domain-lookup  
#### c.	Assign class as the privileged EXEC encrypted password.  
#### d.	Assign cisco as the console password and enable login.  
#### e.	Assign cisco as the VTY password and enable login.  
line con 0  
 password 7 060506324F41  
line aux 0  
line vty 0 4  
 password 7 070C285F4D06  
 login  
line vty 5 15  
 password 7 070C285F4D06  
 login  
#### f.	Encrypt the plaintext passwords.  
#### g.	Create a banner that warns anyone accessing the device that unauthorized access is prohibited  
banner motd ^C  
anyone accessing the device that unauthorized access is prohibited  
#### h.	Save the running configuration to the startup configuration file.  
S1#  
*Nov 21 11:05:55.453: %GRUB-5-CONFIG_WRITING: GRUB configuration is being updated on disk. Please wait...  
*Nov 21 11:05:56.178: %GRUB-5-CONFIG_WRITTEN: GRUB configuration was written to disk successfully.  
###  Step 6: Create VLANs on S1.  
#### a.	Create and name the required VLANs on switch 1 from the table above.  
S1(config)#vlan 100  
S1(config-vlan)#name Clients  
#### b.	Configure and activate the management interface on S1 (VLAN 200) using the second IP address from the subnet calculated earlier. Additionally, set the default gateway on S1.  
S1(config)#int vl 200  
S1(config-if)#ip ad  
S1(config-if)#ip add  
S1(config-if)#ip address 192.168.1.66 255.255.255.224  
#### c.	Configure and activate the management interface on S2 (VLAN 1) using the second IP address from the subnet calculated earlier. Additionally, set the default gateway on S2  
S2(config)#int vl 1  
S2(config-if)#ip ad  
S2(config-if)#ip add  
S2(config-if)#ip address 192.168.1.98 255.255.255.240  
#### d.	Assign all unused ports on S1 to the Parking_Lot VLAN, configure them for static access mode, and administratively deactivate them. On S2, administratively deactivate all the unused ports  
S1(config)#int ra gi 2/0-3  
S1(config-if-range)#sw  
S1(config-if-range)#switchport mo  
S1(config-if-range)#switchport mode ac  
S1(config-if-range)#switchport mode access  
S1(config-if-range)#sw  
S1(config-if-range)#switchport ac  
S1(config-if-range)#switchport access v  
S1(config-if-range)#switchport access vlan 999  
S1(config-if-range)#shu  
S1(config-if-range)#shutdown  
![alt-текст](https://github.com/DanilChery/CiscoProjectsDHCPv4/blob/main/s1-vlan.png "Текст заголовка логотипа 1")  
### Step 7: Assign VLANs to the correct switch interfaces.  
#### a.	Assign used ports to the appropriate VLAN (specified in the VLAN table above) and configure them for static access mode.  
S1(config)#int gi 1/1  
S1(config-if)#sw  
S1(config-if)#switchport mo ac  
S1(config-if)#switchport mo access  
S1(config-if)#sw  
S1(config-if)#switchport ac  
S1(config-if)#switchport access vl  
S1(config-if)#switchport access vlan 100  
### Step 8: Manually configure S1’s interface F0/5 as an 802.1Q trunk  
#### a.	Change the switchport mode on the interface to force trunking.  
#### c.	As a part of the trunk configuration, set the native VLAN to 1000  
#### d.	As another part of trunk configuration, specify that VLANs 100, 200, and 1000 are allowed to cross the trunk.  
#### e.	Save the running configuration to the startup configuration file.  
#### f.	Verify trunking status.  
S1(config-if)#int gi 1/0  
S1(config-if)#switchport mode trunk  
S1(config-if)#switchport trunk allowed vlan 100,200,999,1000  
S1(config-if)#switchport trunk native vlan 1000  
S1#show interfaces trunk  
## Part 2: Configure and v erify two DHCPv4 Servers on R1  
### Step 1: Configure R1 with DHCPv4 pools for the two supported subnets. Only the DHCP Pool for subnet A is given below  
#### a.	Exclude the first five useable addresses from each address pool.  
R1(config)#ip dhcp excluded-address 192.168.1.1 192.168.1.5 
R1(config)#ip dhcp excluded-address 192.168.1.97 192.168.1.101

#### b.	Create the DHCP pool (Use a unique name for each pool).  
R1(config)#ip dhcp pool  
R1(config)#ip dhcp pool Lan_A  
#### d.	Specify the network that this DHCP server is supporting.  
#### e.	Configure the domain name as ccna-lab.com  
#### f.	Configure the appropriate default gateway for each DHCP pool.  
#### g.	Configure the lease time for 2 days 12 hours and 30 minutes.  
R1(dhcp-config)#netw  
R1(dhcp-config)#network 192.168.1.0 255.255.255.192  
R1(dhcp-config)#doma  
R1(dhcp-config)#domain-name ccna-lab.com  
R1(dhcp-config)#def  
R1(dhcp-config)#default-router 192.168.1.1  
R1(dhcp-config)#le  
R1(dhcp-config)#lease 2 12 30  
#### h.	Next, configure the second DHCPv4 Pool using the pool name R2_Client_LAN and the calculated network, default-router and use the same domain name and lease time from the previous DHCP pool  
ip dhcp pool R2_Client_LAN  
 network 192.168.1.96 255.255.255.240  
 default-router 192.168.1.97  
 domain-name ccna-lab.com  
 lease 2 12 30   
### Step 2: Save your configuration  
#### Save the running configuration to the startup configuration file  
### Step 3: Verify the DHCPv4 Server configuration  
#### a.	Issue the command show ip dhcp pool to examine the pool details  
#### b.	Issue the command show ip dhcp bindings to examine established DHCP address assignments.  
#### c.	Issue the command show ip dhcp server statistics to examine DHCP messages.  
### Step 4: Attempt to acquire an IP address from DHCP on PC-A  
#### a.	Open a command prompt on PC-A and issue the command ipconfig /renew  
#### b.	Once the renewal process is complete, issue the command ipconfig to view the new IP information.  
#### c.	Test connectivity by pinging R1’s G0/0/1 interface IP address.  
VPCS> ip dhcp -r  
DDORA IP 192.168.1.6/26 GW 192.168.1.1  
VPCS> ping 192.168.1.1  
  
84 bytes from 192.168.1.1 icmp_seq=1 ttl=255 time=7.967 ms  
## Part 3: Configure and verify a DHCP Relay on R2  
### Step 1: Configure R2 as a DHCP relay agent for the LAN on G0/0/1  
#### a.	Configure the ip helper-address command on G0/0/1 specifying R1’s G0/0/0 IP address.  
![alt-текст](https://github.com/DanilChery/CiscoProjectsDHCPv4/blob/main/pc-ping.png "Текст заголовка логотипа 1")  
### Step 2: Attempt to acquire an IP address from DHCP on PC-B  
#### a.	Open a command prompt on PC-B and issue the command ipconfig /renew.  
#### b.	Once the renewal process is complete, issue the command ipconfig to view the new IP information  
#### c.	Test connectivity by pinging R1’s G0/0/1 interface IP address.  
#### d.	Issue the show ip dhcp binding on R1 to verify DHCP bindings.  
show ip dhcp binding  
Bindings from all pools not associated with VRF:  
IP address          Client-ID/              Lease expiration        Type  
                    Hardware address/  
                    User name  
192.168.1.6         0100.5079.6668.07       Nov 23 2020 10:56 PM    Automatic  
192.168.1.103       0100.5079.6668.08       Nov 21 2020 10:50 AM    Automatic  
       


[R1-Config](https://github.com/DanilChery/CiscoProjectsDHCPv4/blob/main/lab-dhcp-r1.txt "")  
[r2-Config](https://github.com/DanilChery/CiscoProjectsDHCPv4/blob/main/lab-dhcp-r2.txt "")  
[S1-Config](https://github.com/DanilChery/CiscoProjectsDHCPv4/blob/main/lab-dhcp-s1.txt "")  
[S2-Config](https://github.com/DanilChery/CiscoProjectsDHCPv4/blob/main/lab-dhcp-s2.txt "")  
