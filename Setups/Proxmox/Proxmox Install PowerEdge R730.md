Lets start over with the home lab. I have been planning this a little bit and my power went out for a few hours so I'm going to take this a sign to finally start over and even start over with my raid configuration. 

First thing will be getting into the bios of my PowerEdge server and reconfiguring the raid. 

Steps: 
	1. power off the power edge. 
		Note: I did do a hard power off only because my Router was a VM and i couldn't access the webpage. I am also starting form ground 0. 
		1. plug in mouse, keyboard and display if not already connected. 
	2. While booting up, press F2 to boot into bios. Also watch the display to verify that is the correct key to press. Enter the System Setup. 
		1. Once in, go to Device Settings
		2. Select Raid Controller
		3. Configuration Management
		4. Configure for Non-Raid Disks
			1. Agree to all to delete for all disks
		5. Safe and reboot into F10 LifeCycle Controller
		6. Hardware Configuration
		7. Config Wizard
		8. Raid Configuration
		9. Select the RIAD controller
		10. Select the drives
		11. Select the desired raid or default
		12. Select SAS or SATA
		13. Name the Disk
		14. Next 
		15. Finish
	3. Download the ISO for the OS that will be used. 
	4. Bela etcher 
	5. Plug in the flash drive to the machine you will reimage. 
	6. Reboot the server 
		1. May have to change the boot order to boot the flash drive first. 
	7. Once the display is "Welcome to Proxmox" 
		1. install Proxmox VE (graphical)
		2. Accept the EULA
		3. Verify the target 
		4. Verify the Location
		5. Put in a strong password
		6. Verify the correct input signal, FQDN, IP address, and gateway
			1. FQDN (Fully Qualified Domain Name) pve01.lab.subnetphantom.com
			2. 192.168.1.3 ( I set this IP address because it will be the 3rd most important IP address in my home lab/network.)=\
		7. Go to a web browser and go to the IP address and default port unless you set it differently. In my case it was 192.168.1.3:8006
		8. Login to Proxmox
		9. If it tells you "No Valid Subscription"
			1. Proxmox helper scripts
			2. Locate PBS Post Install
			3. Copy script
			4. paste the script in the shell. pve01(the hostname that was set when the FQDN was set.) > shell 
			5. ```
			   bash -c "$(curl -fsSL https://raw.githubusercontent.com/community-scripts/ProxmoxVE/main/tools/pve/post-pve-install.sh)"
			   ```
			6. Execute within the Proxmox shell 
			   It is recommended to answer “yes” (y) to all options presented during the process.
			7. I will be putting this machine in a cluster so will keep HA enabled. 
			8. Wait and then reboot the machine. 
		Proxmox is now installed on my PowerEdge R730. lets get the NAS working
