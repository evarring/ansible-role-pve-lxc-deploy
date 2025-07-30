pve_lxc_deploy
=========

Ansible role to deploy, configure and destroy lxc containers on Proxmox VE

Requirements
------------

- Proxmox VE api token
- LXC template - feel free to choose the lxc container image from <https://images.linuxcontainers.org/> and make sure you get the cloud one.
  You can use the download tag and pve_lxc_deploy_dl_template variable to automate it.
- proxmoxer python package
- community.proxmox collection

Role Variables
--------------

Proxmox VE host settings:

- pve_lxc_deploy_proxmox_api_ip: "10.0.0.10"
- pve_lxc_deploy_proxmox_api_token_user: "root@pam"
- pve_lxc_deploy_proxmox_api_token_id: "proxmox""
- pve_lxc_deploy_proxmox_api_token_secret: dy8293yruqewbfijbaifb389rfbe
- pve_lxc_deploy_node: "nodename01"

LXC container variables:

```yaml
pve_lxc_deploy_container:
  hostname: "hostname.fqdn.net"
  vmid: 100
  password: "{{ ansible_password }}"
  ostemplate: "local:vztmpl/lxc-almalinux9.tar.xz.tar.xz"
  description: "Example LXC container deployment"
  pool: containers
  searchdomain: "fqdn.net"
  tags: lxc
  nameserver: "1.1.1.1 8.8.8.8"
  netif:
    net0: "name=eth0,gw=192.168.0.1,ip={{ ansible_host }}/24,bridge=vmbr0,tag=69"
  pubkey:
    - "{{ lookup('file', 'files/lxccontainer.pub') }}"
  disk_volume:
    storage: local-lvm
    size: 10
```

If you want to automatically also download a container template, set this variable:

```yaml
pve_lxc_deploy_dl_template:
  storage: local
  content_type: vztmpl
  url: "https://images.linuxcontainers.org/images/almalinux/9/amd64/cloud/20250729_23:08/rootfs.tar.xz"
  template: "lxc-almalinux9.tar.xz"
  force: true
```

Tags used:

- deploy - creates the container
- start - starts the container
- stop - stops the container
- redeploy - stops, destroys and creates the container
- destroy - stops and destroys the container
- download - downloads the template

Example Playbook
----------------

```yaml
---
- name: Deploy LXC containers
  hosts: targetlxccontainer
  become: false
  gather_facts: false

  tasks:
    - name: Import role
      ansible.builtin.include_role:
        name: pve_lxc_deploy
      tags: always
...
```

License
-------

MIT
