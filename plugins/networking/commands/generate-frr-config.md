---
description: Generate FRRouting configuration files
argument-hint: Optional routing requirements
---

You are initiating FRR configuration generation using a structured workflow to create production-ready FRRouting configuration files.

## Workflow Steps

### 1. Gather Requirements

If the user provides specific requirements in their message, use those directly. Otherwise, ask the user for:

**Required Information:**
- Routing protocols needed (BGP, OSPF, IS-IS, RIP, static routes, etc.)
- Router ID (e.g., 10.0.0.1)
- Network type (data center leaf-spine, campus core, WAN edge, etc.)

**Protocol-Specific Information:**

**For BGP:**
- Local ASN (e.g., 65001)
- Neighbor details (IP addresses, remote ASNs)
- Address families (IPv4 unicast, IPv6 unicast, EVPN, etc.)
- Route filtering requirements (prefix lists, route maps)
- BGP authentication (MD5 passwords)
- Communities and AS-path filtering

**For OSPF:**
- OSPF process ID
- Area design (area 0 backbone, additional areas)
- Network statements
- Interface costs and priorities
- Authentication (if needed)
- Area types (stub, NSSA, etc.)

**For IS-IS:**
- NET address
- Level design (Level 1, Level 2, or both)
- Interface metrics
- Authentication

**For BFD:**
- BFD parameters for fast failure detection
- Target protocols (BGP, OSPF, IS-IS)

**Additional Requirements:**
- Static routes needed
- Route redistribution between protocols
- Access lists or prefix lists
- VRF configurations (if multi-tenancy needed)
- Authentication requirements
- Specific routing policies

### 2. Launch frr-config-generator Agent

Use the Task tool to launch the frr-config-generator agent with a detailed prompt containing:

```
Generate FRR configuration files for the following requirements:

[Insert gathered requirements here with all details]

Please provide:
1. Complete /etc/frr/daemons file
2. Complete /etc/frr/frr.conf configuration
3. Any additional configuration files needed
4. Step-by-step deployment procedure
5. Validation commands to verify the configuration
6. Troubleshooting commands
7. Rollback procedure
```

### 3. Review Generated Configuration

When the agent returns the configuration, review it for:
- Correct syntax for FRR version
- Proper routing protocol configuration
- Complete authentication settings
- Required route filtering
- Appropriate logging configuration
- Documentation and comments

### 4. Validate Configuration Syntax

Provide the user with validation commands they should run:

```bash
# Validate FRR configuration syntax
sudo vtysh -c "show running-config" --dry-run

# Check for configuration errors
sudo vtysh -f /etc/frr/frr.conf --dry-run

# Verify daemons file
cat /etc/frr/daemons | grep "yes"
```

### 5. Present Deployment Procedure

Ensure the generated configuration includes a safe deployment procedure:

1. **Backup current configuration**
   ```bash
   sudo cp /etc/frr/frr.conf /etc/frr/frr.conf.backup.$(date +%Y%m%d_%H%M%S)
   sudo cp /etc/frr/daemons /etc/frr/daemons.backup.$(date +%Y%m%d_%H%M%S)
   ```

2. **Deploy new configuration**
   ```bash
   # Copy new daemons file
   sudo cp daemons /etc/frr/daemons

   # Copy new configuration
   sudo cp frr.conf /etc/frr/frr.conf

   # Set correct permissions
   sudo chown frr:frr /etc/frr/frr.conf
   sudo chmod 640 /etc/frr/frr.conf
   ```

3. **Restart FRR services**
   ```bash
   # Restart FRR
   sudo systemctl restart frr

   # Check service status
   sudo systemctl status frr
   ```

4. **Verify configuration**
   ```bash
   # Enter vtysh
   sudo vtysh

   # Show running configuration
   show running-config

   # Show protocol-specific status
   show ip bgp summary           # For BGP
   show ip ospf neighbor         # For OSPF
   show isis neighbor            # For IS-IS
   show ip route                 # Routing table
   ```

### 6. Provide Validation Commands

Include comprehensive validation commands for each configured protocol:

**BGP Validation:**
```bash
# Check BGP summary
show ip bgp summary

# Check BGP neighbors
show ip bgp neighbors

# Check received/advertised routes
show ip bgp neighbors <neighbor-ip> routes
show ip bgp neighbors <neighbor-ip> advertised-routes

# Check BGP communities
show ip bgp community
```

**OSPF Validation:**
```bash
# Check OSPF neighbors
show ip ospf neighbor

# Check OSPF database
show ip ospf database

# Check OSPF interfaces
show ip ospf interface

# Check OSPF routes
show ip route ospf
```

**IS-IS Validation:**
```bash
# Check IS-IS neighbors
show isis neighbor

# Check IS-IS database
show isis database

# Check IS-IS topology
show isis topology
```

**BFD Validation:**
```bash
# Check BFD peers
show bfd peers

# Check BFD peer details
show bfd peer <neighbor-ip>
```

### 7. Include Troubleshooting Commands

Provide troubleshooting commands for common issues:

```bash
# Check FRR daemon status
sudo systemctl status frr

# View FRR logs
sudo journalctl -u frr -f

# Check for configuration errors
sudo vtysh -c "show logging"

# Debug BGP
debug bgp updates
debug bgp neighbor-events

# Debug OSPF
debug ospf events
debug ospf packet all

# Clear BGP sessions (use with caution)
clear ip bgp *
clear ip bgp <neighbor-ip>
```

### 8. Document Rollback Procedure

Ensure rollback procedure is clearly documented:

```bash
# Stop FRR
sudo systemctl stop frr

# Restore backup configuration
sudo cp /etc/frr/frr.conf.backup.YYYYMMDD_HHMMSS /etc/frr/frr.conf
sudo cp /etc/frr/daemons.backup.YYYYMMDD_HHMMSS /etc/frr/daemons

# Restart FRR
sudo systemctl start frr

# Verify rollback
sudo vtysh -c "show running-config"
```

## Best Practices

When generating FRR configurations:

1. **Security First**
   - Always use authentication for routing protocols
   - Implement prefix filtering on BGP sessions
   - Use MD5 authentication for BGP neighbors
   - Limit administrative access with ACLs

2. **Routing Protocol Selection**
   - BGP: For data center fabrics, WAN, and internet connectivity
   - OSPF: For campus networks and enterprise routing
   - IS-IS: For large service provider networks
   - Static routes: For simple scenarios or specific routing needs

3. **High Availability**
   - Configure BFD for fast failure detection
   - Use multiple BGP sessions for redundancy
   - Implement proper OSPF area design
   - Configure appropriate route summarization

4. **Operational Excellence**
   - Include comprehensive logging
   - Document all routing policies
   - Use descriptive neighbor names
   - Maintain configuration version control
   - Test in non-production first

5. **Performance Optimization**
   - Configure appropriate timers
   - Use route summarization
   - Implement route dampening for BGP
   - Optimize prefix limits

## Common Scenarios

### Data Center Leaf-Spine BGP
- Use BGP with eBGP for underlay
- Implement EVPN for overlay
- Configure BFD for fast convergence
- Use route reflectors for scaling

### Campus OSPF Network
- Design multi-area OSPF
- Use area 0 as backbone
- Implement stub areas where appropriate
- Configure OSPF authentication

### Internet Edge BGP
- Implement comprehensive prefix filtering
- Configure BGP communities
- Use local preference and MED
- Implement route dampening
- Filter bogon prefixes

## Notes

- FRR configuration uses vtysh CLI syntax similar to industry-standard routing platforms
- Configuration can be managed via /etc/frr/frr.conf or through vtysh interactive CLI
- Always test routing changes in non-production environments first
- Monitor routing protocol convergence during changes
- Keep backup configurations for quick rollback

## Example Task Invocation

```
generate-frr-config I need BGP configuration for a data center leaf switch with ASN 65001, two spine neighbors (192.168.1.1 AS 65100 and 192.168.1.2 AS 65100), advertising loopback 10.0.0.1/32 and local networks 10.10.0.0/24
```
