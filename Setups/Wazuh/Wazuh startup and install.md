First lets install Ubuntu server 24.04.4
		On Proxmox create VM
			8 cores.
			16gb of ram 
			128gb 
		accept all setting to your preference
			I did select Ubuntu Server (NOT minimized)
		I did have to set the IP address manually. 
			10.123.20.5/24
		The rest should be defaults. 
		Yes install the SSH Server
	
Next update and upgrade the host machine. 

```
sudo apt update && sudo apt upgrade -y
sudo apt install curl net-tools -y
```

Install Wazuh: 
```
curl -sO https://packages.wazuh.com/4.14/wazuh-install.sh
sudo bash ./wazuh-install.sh -a | tee wazuh-install.log
```

The current version is 4.14, Change that number in the curl request based on the new current version by looking at the documentation. 

