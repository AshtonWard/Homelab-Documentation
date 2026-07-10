Mandatory setup
	1. Each node must be unique
	2. Must be able to ping each other machine in a mesh. (All on the same network)
Steps to reproduce: 
1.  Ping the other machine(s) to validate connection
2. nano /etc/hosts 
	1. Add the Ip address, FQDN, hostname
3. On pve01
   ```bash
   pvecm create homelab 
   ```
4. Check status
```bash
pvecm status
```
5. Add the other node(s)
	1. On `pve02`:

```
pvecm add 192.168.1.10
```
	It will ask for the root password of `pve01`.
	