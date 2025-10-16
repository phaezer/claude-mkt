---
description: Generate /etc/network/interfaces configuration files
argument-hint: Optional interface requirements
---

You are initiating /etc/network/interfaces configuration generation using a structured workflow to create production-ready Debian/Ubuntu networking configuration files.

## Workflow Steps

### 1. Gather Requirements

If the user provides specific requirements in their message, use those directly. Otherwise, ask the user for:

**Basic Requirements:**
- Target system (Debian version, Ubuntu version)
- Interfaces to configure (eth0, enp0s3, etc.)
- IP addressing method (static, DHCP, or both)
- DNS nameservers
- Search domains

**For Static IP Configuration:**
- IP address and netmask (e.g., 192.168.1.100/24)
- Gateway IP address
- Additional IP addresses (if needed)

**For VLAN Configuration:**
- VLAN IDs and parent interfaces
- IP addressing for each VLAN
- VLAN naming convention

**For Bridge Configuration:**
- Bridge interfaces to create
- Physical interfaces to attach to bridges
- STP settings (on/off)
- IP addressing for bridges
- Use case (virtualization, container networking)

**For Bond Configuration:**
- Bond interfaces to create
- Physical interfaces to bond
- Bond mode (active-backup, 802.3ad, balance-rr, etc.)
- MII monitoring interval
- Primary interface (for active-backup)

**Advanced Options:**
- MTU settings (jumbo frames)
- Static routes
- Policy routing
- IPv6 configuration
- Pre/post up/down scripts

### 2. Launch interfaces-config-generator Agent

Use the Task tool to launch the interfaces-config-generator agent with a detailed prompt containing:

```
Generate /etc/network/interfaces configuration for the following requirements:

[Insert gathered requirements here with all details]

Please provide:
1. Complete /etc/network/interfaces file content
2. List of required packages to install
3. Step-by-step deployment procedure
4. Validation commands
5. Rollback procedure
6. Comments explaining each section
```

### 3. Review Generated Configuration

When the agent returns the configuration, review it for:
- Correct syntax and indentation
- Loopback interface inclusion
- Proper use of auto/allow-hotplug directives
- No conflicting gateway definitions
- Correct netmask/CIDR notation
- Required package dependencies documented

### 4. Identify Required Packages

Ensure the configuration includes a list of required packages:

**Common Package Requirements:**
```bash
# Base networking (usually pre-installed)
apt-get install ifupdown

# For VLAN support
apt-get install vlan

# For bridge support
apt-get install bridge-utils

# For bonding support
apt-get install ifenslave

# For advanced routing
apt-get install iproute2
```

### 5. Present Deployment Procedure

Ensure the generated configuration includes a safe deployment procedure:

1. **Install Required Packages**
   ```bash
   # Update package lists
   sudo apt-get update

   # Install required packages
   sudo apt-get install -y vlan bridge-utils ifenslave

   # Load kernel modules
   sudo modprobe 8021q          # VLAN support
   sudo modprobe bonding        # Bonding support

   # Make modules load at boot
   echo "8021q" | sudo tee -a /etc/modules
   echo "bonding" | sudo tee -a /etc/modules
   ```

2. **Backup Current Configuration**
   ```bash
   # Backup interfaces file
   sudo cp /etc/network/interfaces /etc/network/interfaces.backup.$(date +%Y%m%d_%H%M%S)

   # Backup current network state
   ip addr show > ~/network-backup-$(date +%Y%m%d_%H%M%S).txt
   ip route show >> ~/network-backup-$(date +%Y%m%d_%H%M%S).txt
   ```

3. **Test Configuration Syntax**
   ```bash
   # Test interface bring-up without actually applying
   sudo ifup --no-act eth0
   sudo ifup --no-act <interface-name>

   # Check for syntax errors in the file
   sudo cat /etc/network/interfaces | grep -E "^(auto|allow-hotplug|iface)"
   ```

4. **Deploy New Configuration**
   ```bash
   # Copy new configuration
   sudo cp new-interfaces /etc/network/interfaces

   # Set correct permissions
   sudo chmod 644 /etc/network/interfaces
   sudo chown root:root /etc/network/interfaces
   ```

5. **Apply Configuration**
   ```bash
   # Method 1: Restart networking service (may cause temporary disconnection)
   sudo systemctl restart networking

   # Method 2: Bring down and up specific interfaces
   sudo ifdown eth0 && sudo ifup eth0

   # Method 3: Reboot (safest for complex changes)
   sudo reboot
   ```

6. **Verify Configuration**
   ```bash
   # Check interface status
   ip addr show

   # Check routing table
   ip route show

   # Test connectivity
   ping -c 4 <gateway-ip>
   ping -c 4 8.8.8.8

   # Check DNS resolution
   nslookup google.com
   ```

### 6. Provide Validation Commands

Include comprehensive validation commands:

**Interface Status:**
```bash
# Show all interfaces
ip addr show

# Show specific interface
ip addr show eth0

# Show interface statistics
ip -s link show eth0

# Check interface up/down state
ip link show | grep "state UP"
```

**Routing Validation:**
```bash
# Show main routing table
ip route show

# Show all routing tables
ip route show table all

# Show specific route
ip route get 8.8.8.8
```

**VLAN Validation:**
```bash
# Check VLAN interfaces
cat /proc/net/vlan/config

# Show VLAN interface details
ip -d link show eth0.100
```

**Bridge Validation:**
```bash
# Show bridge interfaces
brctl show

# Show bridge details
bridge link show

# Check STP status
brctl showstp br0
```

**Bond Validation:**
```bash
# Check bonding status
cat /proc/net/bonding/bond0

# Show bond interface details
ip -d link show bond0
```

### 7. Include Troubleshooting Commands

Provide troubleshooting commands for common issues:

**Interface Not Coming Up:**
```bash
# Check interface configuration
sudo ifquery eth0

# Try manual bring-up with verbose output
sudo ifup -v eth0

# Check system logs
sudo journalctl -u networking -n 50

# Check interface configuration file syntax
sudo ifquery --list
```

**No Network Connectivity:**
```bash
# Check interface status
ip link show

# Check IP addressing
ip addr show

# Check default route
ip route show default

# Check physical link
ethtool eth0

# Test ARP
ip neigh show
```

**VLAN Issues:**
```bash
# Verify VLAN module loaded
lsmod | grep 8021q

# Check VLAN interface
cat /proc/net/vlan/eth0.100

# Manually create VLAN to test
sudo ip link add link eth0 name eth0.100 type vlan id 100
```

**Bridge Issues:**
```bash
# Check bridge configuration
brctl show

# View bridge MAC learning table
brctl showmacs br0

# Check STP state
brctl showstp br0
```

**Bond Issues:**
```bash
# Check bonding module
lsmod | grep bonding

# View bond status
cat /proc/net/bonding/bond0

# Check bond mode and slaves
ip -d link show bond0
```

### 8. Document Rollback Procedure

Ensure rollback procedure is clearly documented:

```bash
# Method 1: Restore backup configuration
sudo cp /etc/network/interfaces.backup.YYYYMMDD_HHMMSS /etc/network/interfaces
sudo systemctl restart networking

# Method 2: Manual interface configuration (temporary)
sudo ip addr add 192.168.1.100/24 dev eth0
sudo ip route add default via 192.168.1.1
sudo ip link set eth0 up

# Method 3: Boot into recovery mode
# Reboot and select recovery mode from GRUB menu
# Edit /etc/network/interfaces manually
# Resume normal boot

# Verify rollback
ip addr show
ip route show
ping -c 4 <gateway-ip>
```

## Best Practices

When generating /etc/network/interfaces configurations:

1. **Always Include Loopback**
   ```
   auto lo
   iface lo inet loopback
   ```

2. **Use auto vs allow-hotplug Appropriately**
   - `auto`: For interfaces that should always come up at boot
   - `allow-hotplug`: For removable devices (USB, wireless)

3. **Consistent Indentation**
   - Use spaces or tabs consistently
   - Indent option lines under iface declarations

4. **Gateway Configuration**
   - Only one default gateway per address family
   - Specify gateway on the primary internet-facing interface

5. **Documentation**
   - Add comments explaining complex configurations
   - Document interface purposes
   - Note any external dependencies

6. **Testing**
   - Always use `ifup --no-act` before applying
   - Test in non-production first
   - Have console access before making changes
   - Keep backup configurations

7. **Modular Configuration**
   - Use `/etc/network/interfaces.d/` for complex setups
   - Separate VLANs, bridges, bonds into different files

## Common Scenarios

### Simple Static IP Server
```
auto lo
iface lo inet loopback

auto eth0
iface eth0 inet static
    address 192.168.1.100
    netmask 255.255.255.0
    gateway 192.168.1.1
    dns-nameservers 8.8.8.8 8.8.4.4
```

### DHCP with Static Route
```
auto eth0
iface eth0 inet dhcp
    up ip route add 10.0.0.0/8 via 192.168.1.254
    down ip route del 10.0.0.0/8 via 192.168.1.254
```

### VLAN Configuration
```
auto eth0
iface eth0 inet manual

auto eth0.100
iface eth0.100 inet static
    address 10.0.100.1
    netmask 255.255.255.0
    vlan-raw-device eth0
```

### Bridge for Virtualization
```
auto br0
iface br0 inet static
    address 192.168.1.10
    netmask 255.255.255.0
    gateway 192.168.1.1
    bridge_ports eth0 eth1
    bridge_stp off
    bridge_fd 0
```

### Active-Backup Bond
```
auto bond0
iface bond0 inet static
    address 192.168.1.10
    netmask 255.255.255.0
    bond-slaves eth0 eth1
    bond-mode active-backup
    bond-miimon 100
    bond-primary eth0
```

## Migration Notes

**For Systems Using Netplan:**
- Ubuntu 17.10+ uses netplan by default
- /etc/network/interfaces is deprecated on these systems
- Consider using generate-netplan-config instead
- If using interfaces file on netplan systems, disable netplan renderer

**Checking Current Network Manager:**
```bash
# Check if netplan is active
ls -la /etc/netplan/

# Check if using systemd-networkd
systemctl status systemd-networkd

# Check if using NetworkManager
systemctl status NetworkManager

# Check if using ifupdown
systemctl status networking
```

## Notes

- /etc/network/interfaces is the traditional Debian/Ubuntu networking configuration
- Widely supported across Debian 6-11 and Ubuntu versions pre-17.10
- Still commonly used for servers and systems requiring fine-grained control
- Requires ifupdown package
- Configuration changes require interface restart or system reboot
- Not all features available with all network managers

## Example Task Invocation

```
generate-interfaces-config I need static IP 192.168.1.50/24 on eth0 with gateway 192.168.1.1, two VLANs (VLAN 100 and 200), and a bridge br0 for KVM
```
