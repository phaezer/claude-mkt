---
description: Review network configurations for errors and best practices
argument-hint: Optional configuration files or paths
---

You are initiating a comprehensive network configuration review using a structured workflow to identify errors, validate best practices, and provide improvement recommendations.

## Workflow Steps

### 1. Gather Configuration Information

If the user provides configuration files or paths, use those directly. Otherwise, ask the user for:

**Configuration Files:**
- File paths to review or paste configuration content directly
- Configuration type (interfaces, netplan, FRR, SONiC, vendor-specific)
- Multiple files if reviewing a complete setup

**Review Scope:**
- Target environment (production, staging, development, lab)
- Criticality level (mission-critical, business-critical, standard)
- Specific concerns or focus areas
- Known issues to investigate

**Context Information:**
- Network architecture type (data center, campus, branch, etc.)
- Scale (number of devices, interfaces, routes)
- Existing vs. new deployment
- Recent changes (if troubleshooting)

### 2. Launch network-architecture-reviewer Agent

Use the Task tool to launch the network-architecture-reviewer agent with a detailed prompt:

```
Review the following network configuration files for errors and best practices:

Configuration Type: [interfaces/netplan/FRR/SONiC/etc.]
Environment: [production/staging/lab]
Criticality: [mission-critical/business-critical/standard]

Configuration Files:
[Paste configuration content or provide file paths]

Please perform a comprehensive review including:

1. Syntax Validation
   - Check for configuration syntax errors
   - Validate proper formatting and indentation
   - Identify typos and common mistakes

2. Logical Validation
   - Verify IP addressing scheme (no conflicts, proper subnetting)
   - Check routing logic and gateway configurations
   - Validate interface relationships
   - Confirm VLAN and network segmentation

3. Best Practices Assessment
   - Evaluate against industry standards
   - Check for recommended practices
   - Assess scalability and maintainability
   - Review documentation quality

4. Common Pitfalls and Anti-patterns
   - Identify single points of failure
   - Check for routing loops or suboptimal routing
   - Look for security vulnerabilities
   - Find performance bottlenecks

5. Recommendations
   - Prioritize issues by severity (Critical, High, Medium, Low)
   - Provide specific corrective actions
   - Suggest improvements and optimizations
   - Include relevant documentation references

Focus areas (if specified): [user-specified concerns]
```

### 3. Analyze Review Output

When the agent returns the review, organize findings by:
- **Critical Issues**: Must fix before deployment
- **High Priority**: Should fix soon
- **Medium Priority**: Fix when convenient
- **Low Priority**: Nice-to-have improvements

### 4. Create Issue Report

Structure the review findings in a clear report format:

**Executive Summary:**
- Overall assessment (Ready/Needs Work/Major Issues)
- Total number of issues by severity
- Top 3-5 critical findings
- Recommendation (Deploy/Fix and Review/Major Rework)

**Detailed Findings:**
For each issue:
```
[SEVERITY] Category: Issue Title

Location: <file>:<line> or <section>

Description:
<Clear description of what's wrong>

Impact:
<Potential consequences if not fixed>

Current Configuration:
<Show problematic configuration snippet>

Recommended Fix:
<Specific corrective action with example>

Reference:
<Link to documentation or best practice guide>
```

### 5. Validate Findings

Review the findings for accuracy:
- Verify each issue is actually a problem in context
- Check that recommendations are appropriate for the environment
- Ensure fixes won't introduce new issues
- Validate that all critical items are caught

### 6. Provide Remediation Plan

Create a prioritized remediation plan:

**Immediate Actions (Critical - Fix Before Deployment):**
1. Issue description and fix
2. Estimated time to fix
3. Testing required

**Short-term Actions (High Priority - Fix Within 1 Week):**
1. Issue description and fix
2. Estimated time to fix
3. Testing required

**Medium-term Actions (Medium Priority - Fix Within 1 Month):**
1. Issue description and fix
2. Estimated time to fix
3. Testing required

**Long-term Improvements (Low Priority - Plan for Future):**
1. Enhancement description
2. Benefits
3. Effort estimate

### 7. Include Validation Commands

Provide commands to validate each fix:

**For Interface Configurations:**
```bash
# Debian/Ubuntu /etc/network/interfaces
sudo ifup --no-act <interface>
sudo ifquery <interface>

# Netplan
sudo netplan --debug try
sudo netplan generate

# Verify after applying
ip addr show
ip route show
```

**For FRR Configurations:**
```bash
# Validate configuration syntax
sudo vtysh -f /etc/frr/frr.conf --dry-run

# Check after applying
sudo vtysh -c "show running-config"
sudo vtysh -c "show ip bgp summary"
sudo vtysh -c "show ip ospf neighbor"
```

**For SONiC Configurations:**
```bash
# Validate JSON syntax
python3 -m json.tool config_db.json
jq . config_db.json

# Check after applying
show running-config
show interfaces status
show ip bgp summary
```

### 8. Document Best Practices

Include best practices reference for the configuration type:

**General Networking:**
- No conflicting default gateways
- Proper subnet sizing with growth room
- Consistent naming conventions
- Comprehensive documentation
- Version control for configurations

**Routing:**
- Authentication on routing protocols
- Route filtering and summarization
- Appropriate protocol selection for scale
- Redundant paths where needed
- Convergence time considerations

**High Availability:**
- Eliminate single points of failure
- Redundancy protocols (VRRP, LACP, etc.)
- Fast failover mechanisms (BFD)
- Tested failure scenarios

**Security:**
- Management network isolation
- Encrypted management protocols (SSH, not Telnet)
- ACLs for traffic control
- Logging and monitoring configured
- Regular security updates

## Review Categories and Checks

### Configuration Syntax and Correctness

**For /etc/network/interfaces:**
- [ ] Valid syntax and indentation
- [ ] Loopback interface configured
- [ ] Proper use of auto/allow-hotplug
- [ ] Valid IP addresses and CIDR notation
- [ ] No gateway conflicts
- [ ] Required packages documented (vlan, bridge-utils, etc.)

**For Netplan:**
- [ ] Valid YAML syntax (spaces, not tabs)
- [ ] version: 2 specified
- [ ] Correct renderer choice
- [ ] Valid CIDR notation
- [ ] Proper interface naming
- [ ] No conflicting gateways

**For FRR:**
- [ ] Valid daemon configuration
- [ ] Correct routing protocol syntax
- [ ] Proper route-map and prefix-list definitions
- [ ] Valid BGP/OSPF configuration
- [ ] Authentication configured

**For SONiC:**
- [ ] Valid JSON syntax
- [ ] Required sections present (DEVICE_METADATA)
- [ ] Correct interface naming for platform
- [ ] Valid feature configuration

### Network Design Best Practices

**IP Addressing:**
- [ ] No IP address conflicts or overlaps
- [ ] Appropriate subnet sizing
- [ ] RFC1918 private addressing used correctly
- [ ] Loopback addresses configured
- [ ] DHCP vs static appropriate for use case

**Routing:**
- [ ] No routing loops
- [ ] Appropriate routing protocol
- [ ] Route summarization where applicable
- [ ] Route filtering configured
- [ ] Redistribution done correctly

**High Availability:**
- [ ] Redundant paths configured
- [ ] Bonding/teaming properly configured
- [ ] Gateway redundancy (VRRP/HSRP)
- [ ] Fast failover (BFD) configured
- [ ] Acceptable convergence time

**Scalability:**
- [ ] Design supports growth projections
- [ ] Efficient routing protocol usage
- [ ] Proper network segmentation
- [ ] VLAN design is scalable
- [ ] Capacity planning considered

### Performance Considerations

- [ ] MTU configured appropriately (jumbo frames if needed)
- [ ] Link speeds set correctly
- [ ] QoS/traffic shaping configured
- [ ] Buffer sizes appropriate
- [ ] TCP optimization if needed

### Operational Considerations

**Documentation:**
- [ ] Clear comments in configuration
- [ ] Design decisions documented
- [ ] Rollback procedures defined
- [ ] Contact information included

**Maintainability:**
- [ ] Consistent naming conventions
- [ ] Logical organization
- [ ] Modular structure
- [ ] Version control friendly

**Monitoring:**
- [ ] Logging configured
- [ ] SNMP community strings secure
- [ ] Syslog destinations configured
- [ ] Interface descriptions present

## Common Issues and Fixes

### Critical Issues

**Multiple Default Gateways:**
```
Problem: Multiple interfaces with gateway defined
Impact: Unpredictable routing behavior
Fix: Remove gateway from secondary interfaces, use static routes instead
```

**IP Address Conflicts:**
```
Problem: Same IP on multiple interfaces or duplicate IPs in network
Impact: Network connectivity failures
Fix: Use unique IPs, implement IPAM system
```

**Routing Loops:**
```
Problem: Routes pointing back to same router
Impact: Packet loops, network outage
Fix: Correct routing table, use route filtering
```

**Invalid Syntax:**
```
Problem: Configuration file won't parse
Impact: Configuration won't apply
Fix: Correct syntax errors, validate before applying
```

### High-Risk Issues

**Missing Route Filtering:**
```
Problem: No prefix lists on BGP sessions
Impact: Route leaks, blackhole traffic
Fix: Implement strict prefix filtering
```

**No Redundancy:**
```
Problem: Single path to critical resources
Impact: Outage if link/device fails
Fix: Add redundant links, use bonding/LACP
```

**Weak Security:**
```
Problem: Telnet, HTTP, SNMPv1/v2c enabled
Impact: Security vulnerabilities
Fix: Use SSH, HTTPS, SNMPv3 only
```

## Best Practices Reference

### Interface Configuration
- Use descriptive interface descriptions
- Configure appropriate MTU for network type
- Enable only required protocols
- Document interface purpose and connections

### Routing Protocols
- **OSPF**: Use area 0 as backbone, proper area design
- **BGP**: Implement route filtering, use prefix lists
- **IS-IS**: Proper NET addressing, level hierarchy
- **All**: Use BFD for fast failure detection

### Network Segmentation
- Separate management from data plane
- Use VLANs for logical separation
- Implement proper inter-VLAN routing
- Document VLAN purposes

## Output Format

The review report should include:

1. **Executive Summary** (1 page)
   - Overall assessment
   - Issue count by severity
   - Key recommendations
   - Deploy/no-deploy decision

2. **Detailed Findings** (detailed pages)
   - Each issue with severity, location, description
   - Impact analysis
   - Recommended fixes with examples

3. **Positive Observations**
   - Good practices found
   - Correct implementations
   - Thoughtful design decisions

4. **Remediation Roadmap**
   - Prioritized action items
   - Fix procedures
   - Testing requirements
   - Timeline estimates

5. **Validation Commands**
   - Commands to test each fix
   - Expected outputs
   - Verification procedures

## Notes

- Review is advisory - final decisions rest with network team
- Consider environment context (lab vs production)
- Balance technical perfection with operational reality
- Provide constructive, actionable feedback
- Include references to official documentation
- Test recommendations before applying to production

## Example Task Invocation

```
review-network-config Please review my FRR BGP configuration for a data center leaf switch. Config file is at /etc/frr/frr.conf and I'm seeing some routes not being advertised to spine neighbors. Environment is production, mission-critical.
```
