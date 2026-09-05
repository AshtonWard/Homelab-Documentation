1. Install the VM
	1. Stuff goes here
2. Before we run the VM 
	1. We need to add an additional network interface. One or WAN and one for LAN. 
		![[Pasted image 20260624194056.png]]
		![[Pasted image 20260624195415.png]]
		![[Pasted image 20260624195216.png]]
	2. I also upgraded the ram to 8gb. (8192mb)
3. Boot the VM.
4. ==Log in as installer first==
	1. Install with ZFS (Its better)
	2. I think I accepted the rest as default 
	3. Change the root password. 
5. Assign the interfaces
	1. enter '1'
	2. N to LAGGs
	3. N to VLANs, well do this later.
	4. For me, the WAN interface will be Vtnet0, and the LAN will be Vtnet1
	5. No additional interfaces and then accept those settings
6. Set the Interface IP Addresses
	1. For the WAN
		1. Select Yes for DHCP so it gathers from either ISP or the Router you have in your network already. 
	2. Change the Ip address of the LAN network to 10.123.0.1/24
		1. Nothing for the LAN IPv4 upstream gateway because we didn't change the WAN only the LAN
		2. No IPv6
		3. Don't enable DHCP on LAN, we'll do this in the GUI
		4. leave as HTTPS. 
		5. Don't restore the defaults for the time being
		6. I can now access the web gui by going to 10.123.0.1 but i must plug in an ethernet port into the back of the server on eth port 3 because that is what the Vtnet is assigned to via the interface and Proxmox Linux bridge. 
		7. Also make sure that the network settings in Proxmox are correct. Take a look here: [Adding a new network card] 
		8. Go to the web server
			 ![[Pasted image 20260624203712.png|569]]
7. Enable Dark Mode: 
	1. System > Settings > General > Theme 
		1. opnsense-dark
	2. Note: 
		1. This page will be useful when you need to set DNS Servers like Cloudflare or Google.