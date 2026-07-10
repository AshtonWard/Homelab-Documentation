Start with SSH into the Ansible machine. Then use the same command to SSH into the other machines that will be managed by Ansible.  
```bash
ssh <user>@<ip address>
```
put in the password. 
exit, then run 
```bash
ssh-copy-id <user>@<ip address>
```
and put in the password. Once able to log in without needing to use the password then that machine can be added to the inventory.

I have all my machines held in github. 
Run a ping to verify that ansible works as intended. 
```
ansible proxmox -i inventory.ini -m ping
```
`proxmox` is the group I am running the test against. 
`-i` is the flag for in the inventory file. 
`-m` is the flag for module, ping is the module. 

Passing result: 
```bash
pve01 | SUCCESS => {
    "ansible_facts": {
        "discovered_interpreter_python": "/usr/bin/python3"
    },
    "changed": false,
    "ping": "pong"
}
```

From here we can continue to add machines and fill out the inventory.ini.

