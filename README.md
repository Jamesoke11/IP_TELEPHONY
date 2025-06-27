# IP_TELEPHONY
This demonstrates the configuration of IP phones on a network.  
#SKILLS TESTED
1. IP_telephony configuration
2. DHCP configuration
3. Access Control List configuration
4. Configuration of remote access via SSH

Network: 10.10.10.0/24
Gateway(Fa0/0): 10.10.10.1/24
Pool-Name: IP-PHONES
Excluded Addresses: 10.10.10.1 - 10.10.10.10
 
#ON THE ROUTER:  
#Used Router 2811 because it supports Telephony Service  
STEP1: Configure the interface connecting to the IP-Phone network(Fa0/0)  
	ip address 10.10.10.1 255.255.255.0  
	no shutdown  

STEP2: Configure the DHCP Server on the router  
i. First exclude some addresses from being given out( This is specifically meant for devices whose ip addresses don't need to keep changing for easy administrative management)  
	ip dhcp excluded-address 10.10.10.1 10.10.10.10  
ii. Create the DHCP pool  
	ip dhcp pool IP-PHONES  
	network 10.10.10.0 255.255.255.0  
	default-router 10.10.10.1  
	option 150 ip 10.10.10.1  
STEP3: Configure the call manager telephony service  
i. Enable Telephony service in global configuration mode  
	telephony-service  
ii. Define the maximum numer of directory numbers  
	max-dn 10  
iii. Define the maximum number of phones to be used in the network  
	max-ephones 10  
iv. Specify the ip source address and the port number  
	ip source-address 10.10.10.1 port 2000  
v. Specify the extension numbers to be automatically assigned to buttons  
	auto assign 1 to 10  
vi. Configure the phone directory and provide a phone number for each IP Phone  
	ephone-dn 1  
	number 11111  

#ON THE SWITCH:  
Configure the voice vlan on the switch  
 	interface range f0/1-24, g0/1-2  
	switchport voice vlan 1  

#PASSWORDS USED  
 Privilege mode: cisco  
 SSH: username= james, secret= cisco1  
 
#ACCESS CONTROL LISTS(ACL)  
#ACL to only allow one host(Laptop) to SSH the router  
 ip access-list standard ROUTER-ACCESS  
 permit host 10.10.10.3  
 deny any  
 #Apply the ACL  
 line vty 0 15  
 access-class ROUTER-ACCESS in  
 
#ACL to only allow one host(PC) to SSH the switch  
 ip access-list standard SWITCH-ACCESS  
 permit host 10.10.10.4  
 deny any  
 #Apply the ACL  
 line vty 0 15  
 access-class SWITCH-ACCESS in  

#SSH  
ip domain name SECURE-ACCESS  
crypto key generate rsa  
  1024  
username james secret cisco1  
line vty 0 15  
login local  
transport input ssh  
