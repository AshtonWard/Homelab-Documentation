First we want to make sure we have an understanding of out structure should be. 
VLAN10 - MANAGEMENT
VLAN20 - SECURITY
VLAN30 - SERVICES
VLAN40 - GAME SERVICES
VLAN50 - USER SPACE
VLAN90 - LAB NETWORK

Start by going into Interfaces  > Devices > VLAN 
Here, click add 
		- Leave device blank, allow it to populate automatically (I did manually set vlan90 as vlan09 for the device for continuity)
		- Parent, I want my VLANs in my LAN net so i uses the LAN interface card. My LAN port will run to a switch and i want them to be separated at the switch. 
		- VLAN tag I set based on what VLAN i was setting up. 10, 20, 30... etc.
		- Priority is subjective. 
		- description is set as listed above. 
	Be sure to click apply!!
Next go to Interfaces  > Assignments > + Assign a new interface. 
	- click the VLAN you want to assign, name it VLAN20 (respectfully to the VLAN you are setting. VLAN10, VLAN20, VLAN30... etc.) 
	- Once they are created, they should be configured. 
		- click on the interface to program. 
			- Enable the Interface.
			- I am turning the IPv4 Configuration type to static and leaving IPv6 as none. 
			- At the bottom, this will set the IP address for the VLAN. We are programming VLAN20 for example:  10.0.20.1
			- Save and apply changes
Firewall > rules: 
	Select the VLAN(s) you set up, add a rule, make sure it says PASS, an IN then save. 
		this will allow access to the hosts on this VLAN. 
	Apply changes. 
DHCP: Services > Dndmasq DNS & DHCP > DHCP Ranges
	- add new
	- select the interface, 
	- Input start address, 10.0.20.1
	- End address, 10.0.20.254
	- Subnet mask is 255.255.255.0
	- Input description, I can see i may have to change this later. 
	Then clone and edit for each other VLAN you need to setup. 
	For VLAN 90, I do not want DHCP so I will not create an entry there. 
Apply!