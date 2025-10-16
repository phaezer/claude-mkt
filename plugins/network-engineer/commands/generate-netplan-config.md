---
description: Generate netplan YAML configuration files
argument-hint: Optional netplan requirements
---

You are initiating netplan configuration generation using a structured workflow to create production-ready netplan YAML configuration files for modern Ubuntu and Debian systems.

## Workflow Steps

### 1. Gather Requirements

If the user provides specific requirements in their message, use those directly. Otherwise, ask the user for:

**Basic Requirements:**
- Target system (Ubuntu version 17.10+, Debian with netplan)
- Renderer preference (networkd for servers, NetworkManager for desktops)
- Interfaces to configure (eth0, enp0s3, wlan0, etc.)
- IP addressing method (static, DHCP, or both)
- DNS nameservers
- Search domains

**For Static IP Configuration:**
- IP address and CIDR (e.g., 192.168.1.100/24)
- Gateway IP address (or use routes with "to: default")
- Additional IP addresses (if needed)

**For VLAN Configuration:**
- VLAN IDs and parent interfaces
- IP addressing for each VLAN
- VLAN naming convention (e.g., vlan100 or eth0.100)

**For Bridge Configuration:**
- Bridge interfaces to create
- Physical interfaces to attach to bridges
- STP settings (true/false)
- Forward delay settings
- IP addressing for bridges
- Use case (virtualization, container networking)

**For Bond Configuration:**
- Bond interfaces to create
- Physical interfaces to bond
- Bond mode (active-backup, 802.3ad, balance-rr, balance-xor, etc.)
- MII monitor interval
- Primary interface (for active-backup mode)
- LACP rate (for 802.3ad)

**For WiFi Configuration:**
- SSID and password
- Security type (WPA2, WPA3)
- DHCP or static configuration

**Advanced Options:**
- MTU settings (jumbo frames for 9000 MTU)
- Static routes with metrics
- Routing policy rules
- IPv6 configuration
- Optional interfaces (don't wait at boot)
- DHCP overrides (use-dns, use-routes, etc.)

### 2. Launch netplan-config-generator Agent

Use the Task tool to launch the netplan-config-generator agent with a detailed prompt containing:

```
Generate netplan YAML configuration for the following requirements:

[Insert gathered requirements here with all details]

Please provide:
1. Complete netplan YAML file content
2. Recommended filename (e.g., /etc/netplan/01-network-config.yaml)
3. Step-by-step deployment procedure
4. Validation commands with netplan try
5. Rollback procedure
6. Comments explaining each section
7. Any version-specific considerations
```

### 3. Review Generated Configuration

When the agent returns the configuration, review it for:
- Valid YAML syntax (proper indentation with spaces, not tabs)
- Includes `version: 2` at the top
- Correct renderer specification
- Proper CIDR notation for IP addresses
- No conflicting gateway definitions
- Appropriate use of modern routes vs deprecated gateway4/gateway6
- Correct interface naming for the target system

### 4. Validate YAML Syntax

Before deployment, ensure YAML syntax validation:

```bash
# Install YAML linter if needed
sudo apt-get install -y yamllint

# Validate YAML syntax
yamllint /etc/netplan/01-network-config.yaml

# Or use Python JSON tool
python3 -c "import yaml; yaml.safe_load(open('/etc/netplan/01-network-config.yaml'))"

# Netplan's own syntax check
sudo netplan generate
```

### 5. Present Deployment Procedure

Ensure the generated configuration includes a safe deployment procedure:

1. **Backup Current Configuration**
   ```bash
   # Backup all existing netplan files
   sudo mkdir -p /etc/netplan/backup
   sudo cp /etc/netplan/*.yaml /etc/netplan/backup/

   # Backup current network state
   ip addr show > ~/network-backup-$(date +%Y%m%d_%H%M%S).txt
   ip route show >> ~/network-backup-$(date +%Y%m%d_%H%M%S).txt
   ```

2. **Create New Configuration File**
   ```bash
   # Create new netplan configuration
   sudo nano /etc/netplan/01-network-config.yaml

   # Paste the generated YAML configuration
   # Save and exit (Ctrl+X, Y, Enter)

   # Set correct permissions
   sudo chmod 600 /etc/netplan/01-network-config.yaml
   sudo chown root:root /etc/netplan/01-network-config.yaml
   ```

3. **Remove Conflicting Configurations (if any)**
   ```bash
   # List existing netplan files
   ls -la /etc/netplan/

   # Remove old cloud-init config if replacing it
   # sudo rm /etc/netplan/50-cloud-init.yaml

   # Or disable cloud-init network config
   # echo "network: {config: disabled}" | sudo tee /etc/cloud/cloud.cfg.d/99-disable-network-config.cfg
   ```

4. **Test Configuration with Auto-Revert**
   ```bash
   # Try configuration with 120-second auto-revert
   sudo netplan try

   # The configuration will be applied
   # If you lose connectivity, it auto-reverts after 120 seconds
   # If everything works, press Enter to accept

   # For debug output
   sudo netplan --debug try
   ```

5. **Apply Configuration Permanently**
   ```bash
   # After successful try, apply permanently
   sudo netplan apply

   # Or with debug output
   sudo netplan --debug apply
   ```

6. **Verify Configuration**
   ```bash
   # Check interface status
   ip addr show

   # Check routing table
   ip route show

   # Check IPv6 routes
   ip -6 route show

   # Test connectivity
   ping -c 4 <gateway-ip>
   ping -c 4 8.8.8.8

   # Check DNS resolution
   resolvectl status
   nslookup google.com
   ```

### 6. Provide Validation Commands

Include comprehensive validation commands:

**Netplan Status:**
```bash
# Generate backend configuration
sudo netplan generate

# Check generated configuration
sudo netplan get

# Show current netplan configuration
cat /etc/netplan/*.yaml
```

**Interface Status:**
```bash
# Show all interfaces with networkctl (systemd-networkd)
networkctl status

# Show specific interface
networkctl status eth0

# Traditional ip command
ip addr show
ip link show

# Show interface statistics
ip -s link show eth0
```

**Routing Validation:**
```bash
# Show IPv4 routes
ip route show

# Show IPv6 routes
ip -6 route show

# Show all routing tables
ip route show table all

# Test specific route
ip route get 8.8.8.8
```

**DNS Validation:**
```bash
# Check DNS configuration (systemd-resolved)
resolvectl status

# Check resolv.conf
cat /etc/resolv.conf

# Test DNS resolution
nslookup google.com
dig google.com
```

**VLAN Validation:**
```bash
# Show VLAN interfaces
ip -d link show type vlan

# Show specific VLAN
ip -d link show vlan100
```

**Bridge Validation:**
```bash
# Show bridge interfaces
ip -d link show type bridge

# Show bridge details with bridge command
bridge link show

# Check bridge forwarding database
bridge fdb show
```

**Bond Validation:**
```bash
# Show bond interfaces
ip -d link show type bond

# Check bonding status (if using kernel bonding)
cat /proc/net/bonding/bond0

# Show bond details
ip -d link show bond0
```

**WiFi Validation:**
```bash
# Show WiFi status
nmcli device wifi list

# Show connection status
nmcli connection show

# Check WiFi interface
iw dev wlan0 info
```

### 7. Include Troubleshooting Commands

Provide troubleshooting commands for common issues:

**Configuration Not Applying:**
```bash
# Debug netplan apply
sudo netplan --debug apply

# Check netplan logs
sudo journalctl -u systemd-networkd -n 50

# For NetworkManager renderer
sudo journalctl -u NetworkManager -n 50

# Generate configuration manually
sudo netplan generate

# Check generated files
ls -la /run/netplan/
cat /run/netplan/*.network
```

**YAML Syntax Errors:**
```bash
# Validate YAML syntax
yamllint /etc/netplan/*.yaml

# Check for tabs vs spaces
cat -A /etc/netplan/01-network-config.yaml | grep $'\t'

# Python YAML validation
python3 -c "import yaml; yaml.safe_load(open('/etc/netplan/01-network-config.yaml'))"
```

**Interface Not Coming Up:**
```bash
# Check interface status
networkctl status eth0

# Check systemd-networkd status
sudo systemctl status systemd-networkd

# Restart networkd
sudo systemctl restart systemd-networkd

# Check for interface errors
ip link show eth0

# Check kernel messages
dmesg | grep eth0
```

**No Network Connectivity:**
```bash
# Check IP address assignment
ip addr show

# Check default route
ip route show default

# Check physical link
ethtool eth0

# Test ARP
ip neigh show

# Check firewall
sudo iptables -L -n -v
```

**DNS Not Working:**
```bash
# Check systemd-resolved status
sudo systemctl status systemd-resolved

# Restart resolved
sudo systemctl restart systemd-resolved

# Check DNS servers
resolvectl status

# Check resolv.conf symlink
ls -la /etc/resolv.conf

# Test DNS manually
dig @8.8.8.8 google.com
```

**Renderer Issues:**
```bash
# Check which renderer is active
ps aux | grep -E "networkd|NetworkManager"

# Switch from networkd to NetworkManager
sudo systemctl stop systemd-networkd
sudo systemctl disable systemd-networkd
sudo systemctl start NetworkManager
sudo systemctl enable NetworkManager

# Then update netplan renderer to NetworkManager
```

### 8. Document Rollback Procedure

Ensure rollback procedure is clearly documented:

```bash
# Method 1: Restore backup configuration
sudo cp /etc/netplan/backup/*.yaml /etc/netplan/
sudo netplan apply

# Method 2: Manual configuration (temporary - survives until reboot)
sudo ip addr add 192.168.1.100/24 dev eth0
sudo ip route add default via 192.168.1.1
sudo ip link set eth0 up

# Method 3: Boot with old configuration
# If netplan try timed out, old config is automatically restored

# Method 4: Edit from recovery mode
# Reboot into recovery mode if remote access is lost
# Edit /etc/netplan/*.yaml
# Run: netplan apply
# Resume normal boot

# Verify rollback
ip addr show
ip route show
ping -c 4 <gateway-ip>
```

## Best Practices

When generating netplan configurations:

1. **Use Correct YAML Syntax**
   - Always use spaces for indentation (typically 2 spaces)
   - Never use tabs
   - Use proper list syntax with dashes
   - Quote special characters in SSIDs

2. **File Naming Convention**
   - Use numeric prefixes: 01-, 02-, 10-, 50-
   - Files processed in lexicographical order
   - Example: `/etc/netplan/01-network-config.yaml`
   - Cloud-init uses 50-, so use 01- to override

3. **Renderer Selection**
   - `networkd`: For servers, VMs, containers (lightweight)
   - `NetworkManager`: For desktops, laptops (feature-rich GUI)

4. **Modern vs Legacy Syntax**
   - Prefer `routes` over deprecated `gateway4`/`gateway6`
   - Use `nameservers` for DNS configuration
   - Use `addresses` with CIDR notation

5. **Security**
   - Set file permissions to 600 (only root readable)
   - Don't commit files with WiFi passwords to public repos
   - Use separate files for sensitive configurations

6. **Testing Strategy**
   - Always use `netplan try` before `netplan apply`
   - Test in non-production first
   - Have console/KVM access before making changes
   - Keep backup configurations

7. **Documentation**
   - Add YAML comments for complex configurations
   - Document interface purposes
   - Note any quirks or special requirements

## Common Scenarios

### Simple Static IP Server (Modern Syntax)
```yaml
network:
  version: 2
  renderer: networkd
  ethernets:
    eth0:
      addresses:
        - 192.168.1.100/24
      routes:
        - to: default
          via: 192.168.1.1
      nameservers:
        addresses:
          - 8.8.8.8
          - 8.8.4.4
```

### DHCP Configuration
```yaml
network:
  version: 2
  renderer: networkd
  ethernets:
    eth0:
      dhcp4: true
      dhcp6: false
```

### VLAN Configuration
```yaml
network:
  version: 2
  renderer: networkd
  ethernets:
    eth0:
      dhcp4: false
  vlans:
    vlan100:
      id: 100
      link: eth0
      addresses:
        - 10.0.100.1/24
    vlan200:
      id: 200
      link: eth0
      addresses:
        - 10.0.200.1/24
```

### Bridge for Virtualization
```yaml
network:
  version: 2
  renderer: networkd
  ethernets:
    eth0:
      dhcp4: false
    eth1:
      dhcp4: false
  bridges:
    br0:
      interfaces:
        - eth0
        - eth1
      addresses:
        - 192.168.1.10/24
      routes:
        - to: default
          via: 192.168.1.1
      parameters:
        stp: false
        forward-delay: 0
```

### Bond Configuration (LACP)
```yaml
network:
  version: 2
  renderer: networkd
  ethernets:
    eth0:
      dhcp4: false
    eth1:
      dhcp4: false
  bonds:
    bond0:
      interfaces:
        - eth0
        - eth1
      addresses:
        - 192.168.1.10/24
      routes:
        - to: default
          via: 192.168.1.1
      parameters:
        mode: 802.3ad
        mii-monitor-interval: 100
        lacp-rate: fast
```

### WiFi Configuration
```yaml
network:
  version: 2
  renderer: NetworkManager
  wifis:
    wlan0:
      access-points:
        "MyNetwork":
          password: "securepassword"
      dhcp4: true
```

## Version Compatibility

**Netplan 0.103+:**
- Prefer `routes` with `to: default` over `gateway4`/`gateway6`
- Better routing policy support

**Ubuntu Versions:**
- Ubuntu 17.10+: Netplan by default
- Ubuntu 20.04 LTS: Netplan 0.99+
- Ubuntu 22.04 LTS: Netplan 0.103+
- Ubuntu 24.04 LTS: Latest netplan features

**Renderer Availability:**
- `systemd-networkd`: Available on all netplan systems
- `NetworkManager`: May need installation on server editions

## Migration Notes

**From /etc/network/interfaces:**
- Netplan uses YAML instead of interfaces file format
- Convert `iface eth0 inet static` to YAML ethernets section
- Convert bond/bridge syntax to netplan parameters
- Update scripts to use `netplan apply` instead of `ifup/ifdown`

**From NetworkManager (GUI Config):**
- Export existing connections to netplan format
- Use `renderer: NetworkManager` to keep using GUI
- Or migrate to `renderer: networkd` for server deployments

## Notes

- Netplan is a network configuration abstraction for Ubuntu 17.10+
- Generates backend configuration for NetworkManager or systemd-networkd
- YAML configuration stored in `/etc/netplan/`
- Changes require `netplan apply` to take effect
- `netplan try` is the safest way to test changes (auto-reverts after timeout)
- Files processed in lexicographical order (01-, 02-, etc.)
- Cloud-init may create 50-cloud-init.yaml - use 01- to override

## Example Task Invocation

```
generate-netplan-config I need Ubuntu 22.04 server with static IP 192.168.1.50/24 on enp0s3, gateway 192.168.1.1, two VLANs (100 and 200 on enp0s3), DNS 8.8.8.8, using networkd renderer
```
