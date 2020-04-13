```yaml

# Firewall configuration.
firewall_allowed_tcp_ports:
  - 22
  # For Gluster.
  - 111
  # Port-mapper for Gluster 3.4+.
  # - 2049
  # Gluster Daemon.
  - 24007
  # 24009+ for Gluster <= 3.3; 49152+ for Gluster 3.4+.
  - 24009
  - 24010
  # Gluster inline NFS server.
  - 38465
  - 38466
firewall_allowed_udp_ports:
  - 111

```


Notes on finding nodes in glusterfs_box group:


### Detecting real host ip address for outgoing communications

a) Fron inventory address (for ipv4 only)

```
- debug: var=hostvars[inventory_hostname]['ansible_env'].SSH_CONNECTION.split(' ')[2]
```

or, if your inventory is defined like

```
all: # keys must be unique, i.e. only one 'hosts' per group
    hosts:
        master:
            ansible_host: 192.168.57.101
            ansible_user: serverbuddy
```

You can just refer `ansible_host` variable


b) From default interface

This is where usual confusions happen, when default interface is not what you need

```
- debug: var=hostvars[inventory_hostname]['ansible_default_ipv4']['address']
```

c) Most often used other interfaces are enp0s3 and eth0

```
    # Getting the IP address of enp0s3 interface
    - debug: var=ansible_enp0s3.ipv4.address
    # Getting the IP address of eth0 interface
    - debug: var=ansible_eth0.ipv4.address

d) When you know target mask

```
    - name: Detect swarm advertised ip address
      set_fact:
          swarm_master_address: "{{ item }}"
      when: "item | ipaddr(swarm_address_mask)"
      with_items: "{{ ansible_all_ipv4_addresses }}"
```

### The same - constructing group of the boxes from inventory

a) Fron inventory address (for ipv4 only)

```

- debug: msg="{{ groups['glusterfs_box'] | map('extract', hostvars,  ['ansible_host']) | list  }}"

```

assuming your inventory is defined like

```
all: # keys must be unique, i.e. only one 'hosts' per group
    hosts:
        master:
            ansible_host: 192.168.57.101
            ansible_user: serverbuddy
```

b) From default interface

```
- debug: msg="groups['glusterfs_box'] | map('extract', hostvars, ['ansible_default_ipv4','address']) | list"
```

b) From specific interface

```
    - debug: msg="{{ groups['glusterfs_box'] | map('extract', hostvars,  ['ansible_enp0s8','ipv4', 'address']) | list  }}"
```

