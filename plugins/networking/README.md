# Network Engineer Plugin

A comprehensive Claude Code plugin for architecting and implementing Linux networking infrastructure with support for various networking stacks, routing protocols, and network operating systems.

## Overview

The Network Engineer plugin provides specialized agents and workflows for:
- Network architecture design and planning
- Configuration file generation for multiple Linux networking stacks
- Routing protocol configuration (BGP, OSPF, IS-IS, etc.)
- SONiC Network Operating System management
- Security review based on NIST standards
- Configuration validation and best practices review

## Agents

### network-orchestrator
Main orchestrator agent that coordinates complex network engineering tasks across specialized agents. Use this for multi-step workflows that require coordination between design, configuration generation, and review.

**Use when:**
- Designing complete network architectures
- Deploying configurations that require multiple specialist agents
- Coordinating review and validation workflows

### interfaces-config-generator
Generates `/etc/network/interfaces` configuration files for Debian/Ubuntu systems using the ifupdown networking system.

**Supports:**
- Static and DHCP configuration
- VLANs, bridges, and bonds
- Multiple IP addresses
- Static routes
- MTU configuration

### netplan-config-generator
Generates netplan YAML configuration files for modern Ubuntu/Debian systems.

**Supports:**
- NetworkManager and systemd-networkd renderers
- Static and DHCP configuration
- VLANs, bridges, and bonds
- Advanced routing and routing policy
- IPv4 and IPv6

### network-architecture-reviewer
Reviews network architecture designs and configuration files for errors, best practices, and potential issues.

**Reviews:**
- Configuration syntax and correctness
- IP addressing schemes
- Routing protocol designs
- High availability implementations
- Performance considerations
- Operational best practices

### network-security-reviewer
Performs security reviews of network architectures and configurations based on NIST standards.

**Evaluates:**
- Network segmentation and isolation
- Access control and authentication
- Encryption and data protection
- Routing security (BGP, OSPF, IS-IS)
- DoS protection mechanisms
- Logging and monitoring
- NIST SP 800-53 compliance

### frr-config-generator
Generates FRRouting (FRR) configuration files for routing protocols.

**Supports:**
- BGP (Border Gateway Protocol)
- OSPF (Open Shortest Path First)
- IS-IS (Intermediate System to Intermediate System)
- RIP (Routing Information Protocol)
- BFD (Bidirectional Forwarding Detection)
- Route maps and prefix lists
- Authentication and security

### sonic-engineer
Works with SONiC (Software for Open Networking in the Cloud) Network Operating System for both community and enterprise versions.

**Capabilities:**
- config_db.json generation
- Interface configuration
- VLAN and port channel setup
- BGP configuration
- ACL configuration
- QoS configuration
- Operational commands

## Slash Commands

### /design-network
Design a network architecture with orchestrated planning.

**Usage:**
```
/design-network
```

Creates a comprehensive network design including topology, IP addressing, routing protocols, and redundancy.

### /review-network-config
Review network configuration files for errors and best practices.

**Usage:**
```
/review-network-config
```

Performs detailed configuration review with prioritized findings and recommendations.

### /review-network-security
Security review of network architecture based on NIST standards.

**Usage:**
```
/review-network-security
```

Comprehensive security assessment aligned with NIST SP 800-53 and other standards.

### /generate-frr-config
Generate FRR routing configuration files.

**Usage:**
```
/generate-frr-config
```

Creates production-ready FRR configurations with daemons file and deployment procedures.

### /generate-interfaces-config
Generate /etc/network/interfaces configuration.

**Usage:**
```
/generate-interfaces-config
```

Creates Debian/Ubuntu interfaces configuration with validation steps.

### /generate-netplan-config
Generate netplan YAML configuration.

**Usage:**
```
/generate-netplan-config
```

Creates netplan configuration with renderer selection and validation.

### /sonic-config
Work with SONiC NOS configuration and operations.

**Usage:**
```
/sonic-config
```

Generates SONiC config_db.json or provides operational commands.

### /deploy-network
Deploy network configuration with orchestration.

**Usage:**
```
/deploy-network
```

Coordinates full deployment workflow including generation, review, deployment, and validation.

## Supported Networking Technologies

### Linux Networking Stacks
- `/etc/network/interfaces` (Debian/Ubuntu ifupdown)
- netplan (Ubuntu/Debian with systemd-networkd or NetworkManager)

### Routing Protocols
- BGP (Border Gateway Protocol)
- OSPF (Open Shortest Path First v2 and v3)
- IS-IS (Intermediate System to Intermediate System)
- RIP (Routing Information Protocol)
- Static routing
- BFD (Bidirectional Forwarding Detection)

### Network Operating Systems
- FRRouting (FRR)
- SONiC (Community and Enterprise versions)

### Network Features
- VLANs (802.1Q)
- Link aggregation (bonding/LACP)
- Bridges
- Static routes
- Policy-based routing
- QoS
- ACLs

## Network Commands

The plugin agents can utilize common Linux networking commands:
- `ip` - Network interface and routing management
- `ifup`/`ifdown` - Interface control
- `route` - Routing table management
- `iperf3` - Network performance testing
- `ping`, `traceroute`, `mtr` - Connectivity testing
- `tcpdump`, `ss`, `netstat` - Network diagnostics
- `vtysh` - FRR management
- `config` - SONiC configuration commands

## Example Workflows

### Design and Deploy a BGP Network
```
/design-network

# Provide requirements when prompted
# The orchestrator will:
# 1. Design the network architecture
# 2. Generate FRR BGP configuration
# 3. Generate interface configurations
# 4. Review for best practices
# 5. Review for security (NIST)
# 6. Provide deployment procedures
```

### Generate and Review Interface Configuration
```
/generate-netplan-config

# After generation:
/review-network-config

# If security sensitive:
/review-network-security
```

### Create SONiC Configuration for Data Center
```
/sonic-config

# Specify requirements:
# - Interface configuration
# - VLANs for tenant separation
# - BGP for spine-leaf architecture
# - ACLs for security
```

## Best Practices

### Configuration Management
1. Always backup existing configurations before changes
2. Use version control for configuration files
3. Test configurations in lab environments first
4. Document all design decisions and rationale
5. Maintain rollback procedures

### Security
1. Use authentication on all routing protocols
2. Implement prefix filtering on BGP sessions
3. Configure control plane protection
4. Use encrypted management protocols (SSH, HTTPS)
5. Regular security reviews for production systems

### Deployment
1. Generate configurations using appropriate agents
2. Review with network-architecture-reviewer
3. Security review with network-security-reviewer for production
4. Test in non-production environment
5. Deploy during maintenance windows with rollback plan
6. Validate post-deployment

### High Availability
1. Configure redundant paths and links
2. Use bonding/LACP for link aggregation
3. Implement gateway redundancy (VRRP/HSRP)
4. Use BFD for fast failure detection
5. Test failover scenarios

## Standards and Compliance

### NIST Standards
The network-security-reviewer agent evaluates configurations against:
- NIST SP 800-41 Rev 1: Guidelines on Firewalls and Firewall Policy
- NIST SP 800-53: Security and Privacy Controls
- NIST SP 800-77: Guide to IPsec VPNs
- NIST Cybersecurity Framework (CSF)

### Industry Best Practices
- RFC 1918: Private address space
- RFC 7454: BGP Operations and Security
- RFC 2328: OSPF v2
- RFC 5340: OSPF v3
- RFC 1195: IS-IS for IP
- CIS Benchmarks for network devices

## Installation

This plugin is part of the claude-mkt marketplace. To use:

1. Ensure Claude Code is installed
2. Load this marketplace
3. Agents and slash commands will be available

## Support and Contributions

This plugin supports:
- Debian-based Linux distributions (Debian, Ubuntu)
- FRRouting 7.x and newer
- SONiC Community and Enterprise versions

## License

[Specify your license]

## Version

Version 1.0.0
