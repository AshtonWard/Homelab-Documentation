# Setting Up SSH for Ansible

Ansible's Linux management model is refreshingly simple: the controller connects to the target over SSH and does the work.

That also means SSH needs to work **before** I blame Ansible.

## First Connection

From the Ansible controller:

```bash
ssh <USER>@<HOSTNAME>
```

The first connection lets me verify:

- DNS or address resolution
- routing
- firewall policy
- SSH service availability
- credentials

If ordinary SSH does not work, `ansible -m ping` is not going to rescue the situation.

## Configure Key-Based Authentication

If the target is intended to use the controller's SSH key:

```bash
ssh-copy-id <USER>@<HOSTNAME>
```

Then test again:

```bash
ssh <USER>@<HOSTNAME>
```

The goal is a successful login using the intended key rather than repeatedly typing the target account password.

## Add the Host to Inventory

My original setup used `inventory.ini`. The lab later moved to YAML inventory with `host_vars` and `group_vars`.

A simplified example:

```yaml
all:
  hosts:
    example01:
  children:
    linux:
      hosts:
        example01:
```

Connection-specific values can live in the appropriate variable files instead of being scattered through documentation.

## Test with Ansible

For one host:

```bash
ansible <HOSTNAME> -m ping
```

For a group:

```bash
ansible <GROUP> -m ping
```

A successful result looks roughly like:

```text
example01 | SUCCESS => {
    "changed": false,
    "ping": "pong"
}
```

Ansible's `ping` module is not an ICMP ping. It verifies that Ansible can connect and execute its Python-based module on the target.

## If It Fails

I check in this order:

1. Can the controller resolve the hostname?
2. Can it route to the target?
3. Is the required SSH traffic allowed?
4. Is SSH running on the target?
5. Does normal `ssh` work?
6. Is the correct user/key being used?
7. Is the host in the expected Ansible inventory/group?

Useful commands:

```bash
dig <HOSTNAME>
ssh -v <USER>@<HOSTNAME>
ansible-inventory --graph
ansible-inventory --host <HOSTNAME>
ansible <HOSTNAME> -m ping -vvv
```

## Security Note

I do not store private SSH keys, passwords, or other authentication material in this documentation repository.

The walkthrough should explain the trust model without becoming a collection of everything needed to authenticate to the lab.

## What I Learned

Getting SSH working manually first makes Ansible troubleshooting dramatically easier.

"Ansible is broken" has, on more than one occasion, translated to "DNS is broken," "SSH is blocked," or "I put the host in the wrong group."

Layers matter.
