I bought a OptiPlex 7010 from salvation army and i will boot it of the internet to see what was possibly on it before I image it with Proxmox and add it to my cluster to be my NAS.

1. Plug all connections needed into the machine. 
	1. I went ahead and started the installation to see if the current hard drice was detected. i saw it was a 250GB drive and decided to swap it out for a 2TB drive that i was using for pve01. pve01 is using a 3TB now. 
2. As the machine is booting press F2.
3. Once you are in the bios look for boot menu. 
4. Select UEFI and make sure USB: XXXXXX is selected. first, click apply and exit
5. Load into the Proxmox installer. 
	1. Install Proxmox VE (graphical)
	2. Accept EULA
	3. Verify Drive.
	4. Input Country, Time Zone, and keyboard layout. 
	5. Input password and email
	6. Select Network Interface Card (NIC)
		1. I had an PCIE Card with 1000e available to select.
		2. FQDN (pve02.lab.subnetphantom.com)
		3. Ip Address: 192.168.1.4 
		4. Gateway: 192.168.1.1
		5. DNS Server: 192.168.1.1
	7. Continue and wait for the reboot. One the machine reboots, you may move to the Ip address that you assigned it. As long as it is right
		1. Issue: I could not reach the webserver. 
			1. I consoled into the machine and router. I noticed in my router that it was seen when i changed the ports so the cable that i was using is good. I ran ran 
				```bash
				ip link
				```
				and it displayed that my NICs were all DOWN.
			2. Run this to bring up a NIC:
				```bash 
					ip link set {INTERFACE} up
					cat /etc/network/interfaces
				```
			3. Verify that when you run "ip link" which ever says "UP" should also be the one that is set in "car /etc/network/interfaces" > "bridge-ports: XXXX"
			4. To change it: 
				```bash
					nano /etc/network/interfaces
				```
				Change the port, then save and exit. CTL+S, CTL+X
			5. restert the networking service. 
				```shell
					systemctl restart networking
				```
			6. Verify you can see the ping and webserver. 
	8. Login
	9. If you are met with "No Valid Subscription" search for the PVE Helpter script for PVE Post Install script. 
		```bash
			bash -c "$(curl -fsSL https://raw.githubusercontent.com/community-scripts/ProxmoxVE/main/tools/pve/post-pve-install.sh)"
		```
	10. If you want to put this device in a cluster do not disable HA. otherwise disable all else. 
	11. Wait for update and reboot. 
	12. 