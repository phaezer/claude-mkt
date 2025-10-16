---
description: Deploy network configuration end-to-end
argument-hint: Optional deployment requirements
---

You are orchestrating a comprehensive network deployment workflow using a structured multi-phase approach to ensure safe, validated, and reversible network changes.

## Workflow Steps

### 1. Gather Deployment Requirements

If the user provides deployment details, use those directly. Otherwise, ask the user for:

**Target Environment:**
- Deployment environment (production, staging, development, lab)
- Target systems and platforms (FRR, SONiC, Debian/Ubuntu, etc.)
- Number of devices affected
- Network criticality level (mission-critical, business-critical, standard)

**Configuration Requirements:**
- What configurations to deploy (interfaces, routing, VLANs, etc.)
- Configuration files or requirements (if not already generated)
- Dependencies between configurations
- Order of operations

**Change Window:**
- Scheduled maintenance window (date, time, duration)
- Maximum acceptable downtime
- Business impact of downtime
- Peak vs off-peak considerations

**Rollback Requirements:**
- Rollback criteria (what conditions trigger rollback)
- Maximum time before rollback decision
- Rollback testing requirements
- Configuration backup retention

**Testing and Validation:**
- Pre-deployment tests
- Post-deployment validation criteria
- Automated vs manual testing
- Acceptance criteria

**Communication and Coordination:**
- Stakeholders to notify
- Change control approval
- Team coordination needs
- Communication channels (Slack, email, phone)

### 2. Launch network-orchestrator for Deployment Planning

Use the Task tool to launch the network-orchestrator agent with comprehensive deployment requirements:

```
Orchestrate a network deployment with the following requirements:

Environment: [production/staging/lab]
Criticality: [mission-critical/business-critical/standard]
Target Systems: [list of devices/platforms]

Deployment Requirements:
[Detailed configuration requirements or existing config files]

Change Window:
- Start: [date/time]
- Duration: [hours]
- Max Downtime: [minutes]

Please coordinate a complete deployment workflow including:

1. Pre-Deployment Phase
   - Configuration generation (if needed)
   - Configuration review (architecture)
   - Security review (for production)
   - Change approval process
   - Team preparation and briefing

2. Deployment Preparation
   - Backup current configurations
   - Prepare rollback procedures
   - Stage configuration files
   - Verify access to all devices
   - Test connectivity to management interfaces

3. Deployment Execution
   - Pre-deployment validation
   - Phased deployment procedure
   - Step-by-step deployment commands
   - Checkpoints between phases
   - Progress monitoring

4. Post-Deployment Validation
   - Connectivity tests
   - Routing validation
   - Service validation
   - Performance verification
   - Comprehensive validation commands

5. Rollback Procedures
   - Rollback triggers
   - Rollback execution steps
   - Rollback validation
   - Emergency procedures

6. Documentation
   - Deployment checklist
   - As-built documentation
   - Lessons learned
   - Change log update
```

### 3. Execute Pre-Deployment Phase

Work through pre-deployment requirements:

**Configuration Generation (if needed):**
- Use appropriate specialist agents (frr-config-generator, netplan-config-generator, etc.)
- Generate all required configuration files
- Validate syntax of all configurations

**Configuration Review:**
- Use network-architecture-reviewer agent for technical review
- Identify and fix any configuration errors
- Verify IP addressing, routing logic, redundancy

**Security Review (Production Only):**
- Use network-security-reviewer agent for NIST-based review
- Address critical and high security findings
- Document accepted risks

**Change Approval:**
- Submit change request to CAB (Change Advisory Board)
- Document change rationale and impact
- Obtain necessary approvals
- Communicate change window to stakeholders

### 4. Prepare Deployment Environment

Execute preparation steps:

**Backup Current State:**
```bash
# For each device, backup current configuration
sudo cp /etc/frr/frr.conf /etc/frr/frr.conf.backup.$(date +%Y%m%d_%H%M%S)
sudo cp /etc/network/interfaces /etc/network/interfaces.backup.$(date +%Y%m%d_%H%M%S)
sudo cp /etc/netplan/*.yaml /etc/netplan/backup/
sudo cp /etc/sonic/config_db.json /etc/sonic/config_db.json.backup.$(date +%Y%m%d_%H%M%S)

# Document current state
ip addr show > ~/pre-deploy-state-$(date +%Y%m%d_%H%M%S).txt
ip route show >> ~/pre-deploy-state-$(date +%Y%m%d_%H%M%S).txt
show running-config >> ~/pre-deploy-state-$(date +%Y%m%d_%H%M%S).txt
```

**Stage Configuration Files:**
```bash
# Copy configs to staging directory
sudo mkdir -p /opt/network-configs/deploy-$(date +%Y%m%d)
sudo cp new-configs/* /opt/network-configs/deploy-$(date +%Y%m%d)/

# Verify file integrity
md5sum /opt/network-configs/deploy-$(date +%Y%m%d)/* > checksums.txt
```

**Verify Management Access:**
```bash
# Test SSH access to all devices
for device in device1 device2 device3; do
  echo "Testing $device..."
  ssh -o ConnectTimeout=5 admin@$device "hostname"
done

# Verify console access availability
# Ensure KVM/IPMI/console server access working
```

**Team Coordination:**
- Assemble deployment team
- Brief on deployment plan
- Assign roles (deployment lead, validator, rollback coordinator)
- Test communication channels
- Establish escalation procedures

### 5. Execute Pre-Deployment Validation

Run pre-deployment checks:

**Network Health Check:**
```bash
# Check current connectivity
ping -c 4 <critical-destinations>

# Verify routing
show ip route | include "0.0.0.0/0"
show ip bgp summary
show ip ospf neighbor

# Check interface status
show interfaces status
ip addr show

# Verify no existing issues
show logging | tail -100
```

**Dependency Verification:**
```bash
# Check required packages installed
dpkg -l | grep -E "frr|netplan|vlan|bridge-utils"

# Verify kernel modules loaded
lsmod | grep -E "8021q|bonding"

# Check disk space
df -h /etc

# Verify NTP synchronized
timedatectl status
```

**Go/No-Go Decision:**
- Review all pre-deployment checks
- Confirm team readiness
- Verify change window
- Get final approval to proceed
- Document decision

### 6. Execute Phased Deployment

Deploy configurations in phases with validation between each phase:

**Phase 1: Non-Disruptive Changes**
```bash
# Deploy configurations that won't cause outages
# Examples: interface descriptions, logging, monitoring

# Apply changes
# Validate changes
# Checkpoint - if issues, rollback this phase
```

**Phase 2: Core Infrastructure**
```bash
# Deploy core routing changes
# Configure BGP/OSPF/IS-IS
# Update route policies

# Apply changes in controlled sequence
# Validate routing convergence
# Monitor for issues
# Checkpoint - continue or rollback
```

**Phase 3: Access Layer**
```bash
# Deploy access layer changes
# Configure VLANs, interfaces, ACLs

# Apply changes
# Validate connectivity
# Test user/server access
# Checkpoint - continue or rollback
```

**Phase 4: Final Services**
```bash
# Deploy remaining services
# QoS, monitoring, final optimizations

# Apply changes
# Full validation
# Final checkpoint
```

### 7. Execute Post-Deployment Validation

Comprehensive validation after deployment:

**Connectivity Validation:**
```bash
# Test basic connectivity
ping -c 10 <gateway>
ping -c 10 <critical-server>
ping -c 10 8.8.8.8

# Test from multiple sources
# Verify bidirectional connectivity
```

**Routing Validation:**
```bash
# Verify routing protocols
show ip bgp summary
show ip bgp neighbors
show ip ospf neighbor
show isis neighbor

# Check routing tables
show ip route
show ip route bgp
show ip route ospf

# Verify route advertisement/receipt
show ip bgp neighbors <peer> advertised-routes
show ip bgp neighbors <peer> routes
```

**Interface Validation:**
```bash
# Check all interfaces up
show interfaces status
ip link show | grep "state UP"

# Verify no errors
show interfaces counters errors
ip -s link show

# Check port channels/bonds
show interfaces portchannel
cat /proc/net/bonding/bond0
```

**Service Validation:**
```bash
# Test critical services
curl -I http://<web-service>
nc -zv <database-server> 5432
ssh <jump-host> "uptime"

# Verify DNS
nslookup <critical-hostname>

# Check monitoring
# Verify metrics collection
# Confirm alerts functioning
```

**Performance Validation:**
```bash
# Check latency
ping -c 100 <destination> | tail -1

# Measure throughput (if applicable)
iperf3 -c <test-server>

# Verify no packet loss
ping -c 1000 <destination> | grep "packet loss"
```

**Application Testing:**
- Test critical business applications
- Verify user access
- Check server connectivity
- Validate service dependencies

### 8. Complete Deployment or Execute Rollback

Based on validation results:

**If Successful:**
```bash
# Document successful deployment
# Update as-built documentation
# Update network diagrams
# Update IPAM/inventory
# Notify stakeholders of completion
# Close change ticket
# Schedule post-implementation review
```

**If Rollback Needed:**
```bash
# Execute rollback procedures
sudo cp /etc/frr/frr.conf.backup.YYYYMMDD_HHMMSS /etc/frr/frr.conf
sudo systemctl restart frr

sudo cp /etc/network/interfaces.backup.YYYYMMDD_HHMMSS /etc/network/interfaces
sudo systemctl restart networking

sudo cp /etc/netplan/backup/*.yaml /etc/netplan/
sudo netplan apply

sudo cp /etc/sonic/config_db.json.backup.YYYYMMDD_HHMMSS /etc/sonic/config_db.json
config reload -y

# Validate rollback successful
# Same validation steps as post-deployment

# Document rollback reason
# Schedule root cause analysis
# Plan remediation
```

## Deployment Best Practices

### Change Management

1. **Follow Change Control Process**
   - Submit RFC (Request for Change)
   - Get CAB approval for production
   - Communicate to stakeholders
   - Document all changes

2. **Use Maintenance Windows**
   - Schedule during low-traffic periods
   - Allow sufficient time
   - Have extended window for complex changes
   - Plan for overrun scenarios

3. **Phased Deployment**
   - Deploy in logical phases
   - Validate between phases
   - Checkpoints for go/no-go decisions
   - Ability to rollback individual phases

4. **Test First**
   - Test in lab environment
   - Staging environment validation
   - Pilot deployment (subset of devices)
   - Then full production

### Risk Mitigation

1. **Have Backups**
   - Configuration backups
   - Full system backups where applicable
   - Test restore procedures
   - Off-site backup copies

2. **Rollback Plan**
   - Documented rollback procedures
   - Tested rollback process
   - Clear rollback criteria
   - Rollback time estimates

3. **Team Coordination**
   - Dedicated deployment team
   - Clear roles and responsibilities
   - Communication plan
   - Escalation procedures

4. **Monitoring**
   - Enhanced monitoring during deployment
   - Real-time alerting
   - Performance baselines
   - Anomaly detection

### Communication

1. **Pre-Deployment**
   - Notify stakeholders of change window
   - Communicate expected impact
   - Provide contact information
   - Set expectations

2. **During Deployment**
   - Regular status updates
   - Immediate notification of issues
   - Progress against timeline
   - Any deviations from plan

3. **Post-Deployment**
   - Completion notification
   - Summary of changes
   - Any issues encountered
   - Next steps if applicable

## Deployment Checklist

### Pre-Deployment
- [ ] Configuration files generated and reviewed
- [ ] Architecture review completed
- [ ] Security review completed (production)
- [ ] Change approval obtained
- [ ] Team briefed and assigned roles
- [ ] Backups completed and verified
- [ ] Rollback procedures documented
- [ ] Management access verified
- [ ] Staging environment tested
- [ ] Communication sent to stakeholders

### During Deployment
- [ ] Pre-deployment validation passed
- [ ] Phase 1 deployed and validated
- [ ] Phase 2 deployed and validated
- [ ] Phase 3 deployed and validated
- [ ] Final phase deployed and validated
- [ ] All checkpoints passed
- [ ] No unexpected issues
- [ ] Timeline on track

### Post-Deployment
- [ ] All connectivity tests passed
- [ ] Routing validated
- [ ] Services operational
- [ ] Performance acceptable
- [ ] No errors in logs
- [ ] Monitoring functioning
- [ ] Applications tested
- [ ] Stakeholders notified
- [ ] Documentation updated
- [ ] Change ticket closed

## Deployment Scenarios

### Low-Risk Deployment
- Lab or development environment
- Non-critical systems
- Well-tested configurations
- Easy rollback

**Simplified Process:**
1. Generate configurations
2. Quick review
3. Backup current state
4. Deploy
5. Validate
6. Done

### Medium-Risk Deployment
- Staging or pre-production
- Business-critical (not mission-critical)
- Tested configurations
- Documented rollback

**Standard Process:**
1. Generate and review configurations
2. Change approval (simplified)
3. Backup and prepare
4. Phased deployment with validation
5. Full validation
6. Document

### High-Risk Deployment
- Production environment
- Mission-critical systems
- Complex changes
- High business impact

**Full Process:**
1. Design and plan
2. Generate configurations
3. Architecture review
4. Security review
5. Lab testing
6. Change approval (full CAB)
7. Stakeholder communication
8. Comprehensive backups
9. Team coordination
10. Pre-deployment validation
11. Phased deployment with checkpoints
12. Comprehensive post-deployment validation
13. Documentation and closure
14. Post-implementation review

## Common Deployment Issues

**Configuration Syntax Errors:**
- Validate all configs before deployment
- Use dry-run/test modes
- Have syntax checkers in place

**Connectivity Loss:**
- Always have out-of-band access
- Test on non-production first
- Have console/KVM access ready
- Deploy during maintenance window

**Routing Convergence Issues:**
- Allow time for convergence
- Monitor convergence in real-time
- Have pre-calculated convergence times
- Test failover scenarios

**Rollback Complexity:**
- Keep rollback procedures simple
- Test rollback procedures beforehand
- Have automated rollback scripts
- Clear rollback criteria

## Notes

- Deployments should never be rushed
- Always have a rollback plan
- Test everything in non-production first
- Communication is critical for success
- Document everything
- Learn from each deployment (post-implementation review)
- Automation reduces risk for repetitive deployments

## Example Task Invocation

```
deploy-network Deploy BGP configuration to 10 data center leaf switches in production. We have a 4-hour maintenance window Saturday 2-6am. Need full architecture and security review before deployment. Configs are already generated and need deployment orchestration with phased rollout and comprehensive validation.
```
