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
