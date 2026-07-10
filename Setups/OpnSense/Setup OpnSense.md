1. Install the VM
	1. Stuff goes here
2. Before we run the VM 
	1. We need to add an additional network interface. One or WAN and one for LAN. 
		![[Pasted image 20260624194056.png]]
		![[Pasted image 20260624195415.png]]
		![[Pasted image 20260624195216.png]]
	2. I also upgraded the ram to 8gb. (8192mb)
3. Boot the VM.
4. Assign the interfaces
	1. enter '1'
	2. N to LAGGs
	3. N to VLANs, well do this later.
	4. For me, the WAN interface will be Vtnet0, and the LAN will be Vtnet1
	5. No additional interfaces and then accept those settings
5. Change the Ip address of the LAN network to 10.0.10.1/24
	1. nothing the LAN IPv4 upstream gateway because we didn't change the WAN only the LAN
	2. No IPv6
	3. I do want to enable DHCP on LAN
	4. start is 10.0.10.100 end 10.0.10.200
	5. Change to Http. 
	6. restore the defaults for the time being
	7. I can now access the web gui by going to 10.0.10.1. but i must plug in an ethernet port into the back of the server on eth port 3 becuase that is what the Vtnet is assigned to via the interface and proxmox linux bridge. 
	8. Also make sure that the network settings in proxmox are correct. Take a look here: [Adding a new network card] 
	9. Go to the web server
		 ![[Pasted image 20260624203712.png]]