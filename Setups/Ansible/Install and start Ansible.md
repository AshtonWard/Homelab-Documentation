I want ansible to be the controller of my home lab. when ever I want something done I want it automatically done and install of the different dependencies in my home lab.
	Applications like 
		- Git
		- Wazuh
		- Ansible it self 
		- Controller for Docker
		- Automatic updates
		- CVE management 
		- Much more
	First lets install Ubuntu server 24.04.4
		On Proxmox create VM
			2 cores.
			4gb of ram 
			50gb 
		accept all setting to your preference
			I did select Ubuntu Server (minimized)
		The rest should be defaults. 
		Yes to install the SSH Server
	Once you get into your machines install everything you need: 
	
		sudo apt update && sudo apt install -y ansible openssh-client sshpass python3-pip git curl
		
		once that is done, check and make sure ansible is installed: `ansible --version`


