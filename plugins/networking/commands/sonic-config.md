---
description: Generate SONiC NOS configuration files
argument-hint: Optional SONiC requirements
---

You are initiating SONiC (Software for Open Networking in the Cloud) NOS configuration using a structured workflow to create production-ready SONiC configuration files and operational procedures.

## Workflow Steps

### 1. Gather Requirements

If the user provides specific requirements in their message, use those directly. Otherwise, ask the user for:

**Basic Requirements:**
- SONiC version (community or enterprise/vendor-specific)
- Platform/hardware (Broadcom, Mellanox, Intel, etc.)
- Switch role (Leaf, Spine, ToR, Border, etc.)
- Hostname and basic metadata

**Configuration Type Needed:**
- Interface configuration (physical ports, speeds, MTU)
- VLAN configuration
- Port channel/LAG configuration
- BGP routing configuration
- OSPF routing configuration
- ACL configuration
- QoS configuration
- Loopback interfaces
- Static routes
- System management (NTP, syslog, SNMP)

**For Interface Configuration:**
- Interface names (Ethernet0, Ethernet4, etc.)
- Speeds (10G, 25G, 40G, 100G, 400G)
- Admin status (up/down)
- MTU settings (typically 9100 for data centers)
- FEC settings (RS, FC)

**For VLAN Configuration:**
- VLAN IDs and descriptions
- VLAN member ports
- Tagging mode (tagged/untagged)
- VLAN interface IP addresses

**For Port Channel/LAG:**
- Port channel interface names
- Member interfaces
- LACP configuration
- Minimum links

**For BGP Configuration:**
- Local ASN
- BGP neighbors (IP, ASN, descriptions)
- Peer groups
- Route policies and prefix lists
- Address families (IPv4, IPv6, EVPN)
- Authentication

**For ACL Configuration:**
- ACL table names and types (L3, L2, CTRLPLANE)
- ACL rules (priorities, actions, match criteria)
- Port bindings

**For QoS Configuration:**
- DSCP to TC mapping
- TC to queue mapping
- Scheduler policies
- Port QoS profiles

### 2. Launch sonic-engineer Agent

Use the Task tool to launch the sonic-engineer agent with a detailed prompt containing:

```
Generate SONiC configuration for the following requirements:

[Insert gathered requirements here with all details]

Please provide:
1. Complete config_db.json file
2. Equivalent CLI commands for reference
3. Step-by-step deployment procedure
4. Validation commands specific to this configuration
5. Rollback procedure
6. Any platform-specific notes or requirements
7. Prerequisites (SONiC version, required features)
```

### 3. Review Generated Configuration

When the agent returns the configuration, review it for:
- Valid JSON syntax
- Correct SONiC schema structure
- All required sections present (DEVICE_METADATA, etc.)
- Proper interface naming for the platform
- No conflicting configurations
- Complete BGP/routing configuration
- Appropriate security settings

### 4. Validate JSON Syntax

Before deployment, ensure JSON syntax validation:

```bash
# Validate JSON syntax
python3 -m json.tool config_db.json

# Or use jq
jq . config_db.json

# Check for common issues
jq 'keys' config_db.json  # Show top-level keys
```

### 5. Present Deployment Procedure

Ensure the generated configuration includes a safe deployment procedure:

1. **Backup Current Configuration**
   ```bash
   # Save current running config
   config save -y

   # Create timestamped backup
   sudo cp /etc/sonic/config_db.json /etc/sonic/config_db.json.backup.$(date +%Y%m%d_%H%M%S)

   # Save current state
   show running-config > ~/sonic-config-backup-$(date +%Y%m%d_%H%M%S).txt
   show interfaces status >> ~/sonic-config-backup-$(date +%Y%m%d_%H%M%S).txt
   ```

2. **Validate New Configuration**
   ```bash
   # Validate JSON syntax
   python3 -m json.tool new_config_db.json

   # Validate SONiC config format
   sonic-cfggen -j new_config_db.json --print-data

   # Check for required keys
   jq 'has("DEVICE_METADATA")' new_config_db.json
   ```

3. **Deploy Configuration**
   ```bash
   # Copy new configuration
   sudo cp new_config_db.json /etc/sonic/config_db.json

   # Set correct permissions
   sudo chown root:root /etc/sonic/config_db.json
   sudo chmod 644 /etc/sonic/config_db.json
   ```

4. **Apply Configuration**
   ```bash
   # Method 1: Load configuration without full restart
   config load /etc/sonic/config_db.json -y

   # Method 2: Full configuration reload (restarts services)
   config reload -y

   # Method 3: Load and save
   config load /etc/sonic/config_db.json -y && config save -y
   ```

5. **Verify Configuration**
   ```bash
   # Check interfaces
   show interfaces status

   # Check IP configuration
   show ip interfaces

   # Check BGP (if configured)
   show ip bgp summary

   # Check VLANs (if configured)
   show vlan brief

   # Check port channels (if configured)
   show interfaces portchannel

   # Check system status
   show system-health
   ```

### 6. Provide Validation Commands

Include comprehensive validation commands for each configuration type:

**Interface Validation:**
```bash
# Show all interface status
show interfaces status

# Show specific interface
show interfaces status Ethernet0

# Show interface counters
show interfaces counters

# Show interface errors
show interfaces counters errors

# Show transceiver information
show interfaces transceiver info

# Show interface description
show interfaces description
```

**VLAN Validation:**
```bash
# Show VLAN configuration
show vlan brief

# Show detailed VLAN config
show vlan config

# Show VLAN member ports
show vlan id 100
```

**Port Channel Validation:**
```bash
# Show port channel summary
show interfaces portchannel

# Show LACP status
show lacp neighbor
show lacp internal

# Show port channel details
show interface PortChannel1
```

**BGP Validation:**
```bash
# Show BGP summary
show ip bgp summary

# Show BGP neighbors
show ip bgp neighbors

# Show BGP routes
show ip bgp

# Show received routes from neighbor
show ip bgp neighbors 192.168.1.1 received-routes

# Show advertised routes to neighbor
show ip bgp neighbors 192.168.1.1 advertised-routes

# Show BGP configuration
show runningconfiguration bgp
```

**OSPF Validation:**
```bash
# Show OSPF neighbors
show ip ospf neighbor

# Show OSPF routes
show ip ospf route

# Show OSPF database
show ip ospf database

# Show OSPF interfaces
show ip ospf interface
```

**ACL Validation:**
```bash
# Show ACL tables
show acl table

# Show ACL rules
show acl rule

# Show ACL counters
acl-loader show table
acl-loader show rule
```

**QoS Validation:**
```bash
# Show QoS maps
show qos map dscp-to-tc
show qos map tc-to-queue

# Show queue counters
show queue counters

# Show priority-group
show priority-group
```

**System Validation:**
```bash
# Show system information
show version
show platform summary
show platform syseeprom

# Show services
show services

# Show system health
show system-health

# Show running configuration
show running-config
```

### 7. Include Troubleshooting Commands

Provide troubleshooting commands for common issues:

**Configuration Not Applied:**
```bash
# Check config_db.json syntax
python3 -m json.tool /etc/sonic/config_db.json

# Check SONiC services
show services

# Restart specific service
sudo systemctl restart bgp
sudo systemctl restart swss

# Check service logs
sudo journalctl -u bgp -n 100
sudo journalctl -u swss -n 100

# View syslog
show logging
tail -f /var/log/syslog
```

**Interface Issues:**
```bash
# Check interface admin state
show interfaces status Ethernet0

# Check physical link
show interfaces transceiver info Ethernet0

# Check interface errors
show interfaces counters errors Ethernet0

# Clear interface counters
sonic-clear counters

# Check ASIC programming
show platform switch
```

**BGP Not Establishing:**
```bash
# Check BGP configuration
show runningconfiguration bgp

# Check BGP neighbors
show ip bgp neighbors 192.168.1.1

# Enable BGP debugging
vtysh -c "debug bgp neighbor-events"
vtysh -c "debug bgp updates"

# Check connectivity to neighbor
ping 192.168.1.1

# Check routing table
show ip route
```

**VLAN Issues:**
```bash
# Check VLAN configuration
show vlan config

# Check VLAN member configuration
redis-cli -n 4 HGETALL "VLAN_MEMBER|Vlan100|Ethernet8"

# Check bridge FDB
show mac

# Check VLAN interface
show ip interfaces | grep Vlan
```

**Database Issues:**
```bash
# Access config database (DB 4)
redis-cli -n 4

# Show all keys
redis-cli -n 4 KEYS "*"

# Show specific configuration
redis-cli -n 4 HGETALL "PORT|Ethernet0"
redis-cli -n 4 HGETALL "DEVICE_METADATA|localhost"

# Check application database (DB 0)
redis-cli -n 0 KEYS "*"
```

### 8. Document Rollback Procedure

Ensure rollback procedure is clearly documented:

```bash
# Method 1: Restore from backup
sudo cp /etc/sonic/config_db.json.backup.YYYYMMDD_HHMMSS /etc/sonic/config_db.json
config reload -y

# Method 2: Load previous working config
config load /etc/sonic/config_db.json.backup.YYYYMMDD_HHMMSS -y

# Method 3: Manual configuration via CLI (temporary)
# Use vtysh for routing protocols
sudo vtysh
# Use config commands for interfaces/VLANs
config interface ip add Ethernet0 192.168.1.1/24

# Method 4: Factory reset (CAUTION)
# sudo config-setup factory

# Verify rollback
show interfaces status
show ip bgp summary
show vlan brief
```

## Best Practices

When generating SONiC configurations:

1. **Configuration Management**
   - Always backup before changes
   - Use version control for config_db.json
   - Test in lab environment first
   - Document all changes

2. **Interface Configuration**
   - Use consistent interface naming
   - Configure appropriate MTU for network (9100 for data centers)
   - Enable FEC where appropriate
   - Add meaningful descriptions

3. **Routing Configuration**
   - Use BGP authentication
   - Implement prefix filtering
   - Configure maximum-prefix limits
   - Use BFD for fast convergence

4. **VLAN Design**
   - Plan VLAN ID scheme
   - Use meaningful VLAN descriptions
   - Separate traffic types appropriately
   - Configure VLAN interfaces for L3

5. **High Availability**
   - Configure redundant uplinks
   - Use port channels for link aggregation
   - Implement BFD for fast failure detection
   - Configure multiple BGP sessions

6. **Security**
   - Implement control plane ACLs
   - Use routing protocol authentication
   - Configure management ACLs
   - Enable logging and monitoring

7. **Operational Excellence**
   - Configure NTP for time synchronization
   - Set up syslog to central server
   - Enable SNMP monitoring
   - Use consistent naming conventions

## Common Scenarios

### Data Center Leaf Switch (BGP Unnumbered)
- Underlay BGP with spine neighbors
- VLAN configuration for server access
- Port channels for server bonding
- Loopback for VTEP
- ACLs for security

### Top-of-Rack (ToR) Switch
- Access port configuration for servers
- Uplinks to spine (port channels)
- VLANs for network segmentation
- Basic BGP or OSPF routing
- QoS policies

### Spine Switch
- High-density 100G/400G interfaces
- BGP configuration for all leaf neighbors
- Route reflection (if used)
- Minimal VLANs (management only)
- BFD for fast convergence

### Border/Edge Switch
- External BGP peering
- Route filtering and policies
- ACLs for security
- NAT configuration (if supported)
- Internet routing table handling

## SONiC Architecture Notes

**Key Components:**
- **Redis Database**: Configuration and state storage
- **Docker Containers**: Modular service architecture
- **SAI**: Switch Abstraction Interface for hardware
- **FRR**: Routing protocol daemon (BGP, OSPF, etc.)
- **Orchestration Agent**: Translates config to ASIC

**Database Structure:**
- **ConfigDB** (DB 4): Configuration data
- **AppDB** (DB 0): Application state
- **StateDB** (DB 6): Operational state
- **ASIC_DB** (DB 1): Hardware programming

**Configuration Methods:**
1. config_db.json (recommended for automation)
2. CLI commands (immediate application)
3. OpenConfig/gNMI (enterprise features)
4. REST API (if available)

## Platform Considerations

**Broadcom-based Switches:**
- Common in enterprise and cloud
- SAI fully supported
- Check BCM shell access if needed

**Mellanox-based Switches:**
- Common in high-performance networks
- Spectrum ASIC series
- Check SX-SDK version

**Barefoot/Intel Tofino:**
- Programmable pipeline
- P4 runtime support
- Check platform-specific features

**Interface Naming:**
- Usually Ethernet0, Ethernet4, etc. (increments of 4)
- Check platform documentation for mapping
- Alias field for human-readable names

## Notes

- SONiC uses JSON-based configuration (config_db.json)
- Configuration stored in Redis database
- Supports both CLI and file-based configuration
- Container-based architecture for modularity
- Uses FRR for routing protocols (BGP, OSPF, IS-IS)
- SAI provides hardware abstraction
- Always validate JSON syntax before deployment
- Test routing changes in maintenance windows
- Monitor ASIC programming after changes

## Example Task Invocation

```
sonic-config I need a data center leaf switch configuration with ASN 65001, two spine BGP neighbors (192.168.1.1 and 192.168.1.2 both AS 65100), VLAN 100 for servers on Ethernet8-Ethernet24, loopback 10.0.0.1/32, and port channel with Ethernet0 and Ethernet4 for uplink
```
