I decided I wanted to try out ProxmenuX, so here is my setup guide for it. I do have an extra node in my cluster that ill only be installing it on my second node for comparison sake. 
1. always update: 
	`apt update`
2. Run the stable installer: 
	`bash -c "$(wget -qLO - https://raw.githubusercontent.com/MacRimi/ProxMenux/main/install_proxmenux.sh)"`
	1. I will leave all default.
3. next go to: `http://10.123.10.4:8008/` or what ever the ip address if of your node.
4. Setup password of course. 
	1. I had to make my password extra long ** 
5. I poked around and come across Security updates. thought that was really impressive. Naturally I updated. 

Something to make note of: 
### Web Interfaces

Proxmox VE and ProxMenux Monitor operate as separate web services.

| Port | Service                       | Purpose                        |
| ---- | ----------------------------- | ------------------------------ |
| 8006 | `pveproxy`                    | Proxmox VE Web UI              |
| 8008 | `python3` / ProxMenux Monitor | ProxMenux monitoring interface |

The ProxMenux Monitor runs independently from the Proxmox web interface and automatically starts after a system reboot.