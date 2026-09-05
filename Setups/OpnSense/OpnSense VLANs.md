First we want to make sure we have an understanding of out structure should be. 
VLAN10 - MANAGEMENT
VLAN20 - SECURITY
VLAN30 - SERVICES
VLAN40 - GAME SERVICES
VLAN50 - USER SPACE
VLAN60 - (Undetermined - VOIP)
VLAN70 - (IOT)
VLAN80 - (Unifi Cameras)
VLAN90 - LAB NETWORK

Start by going into Interfaces  > Devices > VLAN 
Click add / plus button 
		- Leave device blank, allow it to populate automatically (I did manually set vlan90 as vlan09 for the device for continuity)
		- Parent, I want my VLANs in my LAN net so i uses the LAN interface card. My LAN port will run to a switch and i want them to be separated at the switch. 
		- VLAN tag I set based on what VLAN i was setting up. 10, 20, 30... etc.
		- Priority is subjective. 
		- Description is set as listed above. 
	Be sure to click apply!!
Next go to Interfaces  > Assignments > + Assign a new interface. 
	- Click the VLAN you want to assign, name it VLAN20 (respectfully to the VLAN you are setting. VLAN10, VLAN20, VLAN30... etc.) 
	- Once they are created, they should be configured. 
		- Click the interface on the left to program. 
			- Enable the Interface.
			- I am turning the IPv4 Configuration type to DHCP and leaving IPv6 off. 
			- At the bottom, this will set the IP address for the VLAN. We are programming VLAN20 for example:  10.123.20.1
			- Save and apply changes
Firewall > Rules: 
	Add new Rule by pressing the plus sign button
		- Select the VLAN(s) you set up using the drop down interface button.
		- Make sure it says PASS, and IN
		- Save. 
		- This will allow access to the hosts on this VLAN. 
			- Just for now I will select all the VLANs I am setting up so i can access all of them when ever I need to. Once my VLANs are set up I will remove this rule and be more selective
	Apply changes. 
DHCP: Services > Dnsmasq DNS & DHCP > DHCP Ranges
	- add new
	- select the interface, 
	- Input start address, 10.123.20.1
	- End address, 10.123.20.254
	- Subnet mask is 255.255.255.0
	- Input description, I can see i may have to change this later. 
	Then clone and edit for each other VLAN you need to setup. 
	For VLAN 90, I do not want DHCP so I will not create an entry there. 
Apply!