---
description: Review network security based on NIST standards
argument-hint: Optional network architecture or config files
---

You are initiating a comprehensive network security review using a structured workflow based on NIST (National Institute of Standards and Technology) standards and industry security best practices.

## Workflow Steps

### 1. Gather Security Review Requirements

If the user provides specific information, use that directly. Otherwise, ask the user for:

**Network Information:**
- Network architecture diagrams or descriptions
- Configuration files (FRR, interfaces, netplan, SONiC, firewall configs)
- Network topology (data center, campus, branch, cloud, hybrid)
- Critical assets and data flows

**Environment Context:**
- Criticality level (mission-critical, business-critical, standard)
- Exposure (internet-facing, internal, DMZ, management)
- Industry and compliance requirements (PCI-DSS, HIPAA, SOC 2, FEDRAMP)
- Specific NIST frameworks to align with

**Security Concerns:**
- Known vulnerabilities or weaknesses
- Recent security incidents
- Specific threats or attack vectors of concern
- Third-party security audit findings

**Scope:**
- Full infrastructure review or specific components
- Focus areas (access control, encryption, routing, etc.)
- Depth of review (high-level vs deep technical)

### 2. Launch network-security-reviewer Agent

Use the Task tool to launch the network-security-reviewer agent with a comprehensive prompt:

```
Perform a comprehensive NIST-based security review of the following network:

Network Type: [data center/campus/branch/etc.]
Criticality: [mission-critical/business-critical/standard]
Exposure: [internet-facing/internal/DMZ]
Compliance Requirements: [PCI-DSS/HIPAA/SOC 2/NIST CSF/etc.]

Network Architecture and Configurations:
[Provide diagrams, descriptions, and configuration files]

Specific Concerns (if any):
[User-specified security concerns]

Please perform a comprehensive security review based on NIST standards covering:

1. Network Segmentation and Isolation (NIST SP 800-41, 800-125B)
   - Trust zone separation
   - DMZ implementation
   - VLAN/VRF isolation
   - Micro-segmentation for critical assets
   - East-west traffic control

2. Access Control and Authentication (NIST SP 800-53 AC Family)
   - Management access security
   - Authentication mechanisms (MFA, SSH keys, TACACS+)
   - Authorization and privilege escalation
   - Session management
   - Role-based access control

3. Encryption and Data Protection (NIST SP 800-52, 800-77, 800-113)
   - Management protocol encryption (SSH vs Telnet)
   - Routing protocol security
   - VPN configuration and cipher suites
   - SNMP security (SNMPv3)
   - WiFi security (WPA3/WPA2 Enterprise)

4. Routing Security (Industry Best Practices + NIST)
   - BGP security (authentication, filtering, RPKI)
   - OSPF/IS-IS authentication
   - Route filtering and validation
   - Prevention of route hijacking
   - Bogon filtering

5. Denial of Service Protection (NIST Principles)
   - Rate limiting and CoPP
   - SYN flood protection
   - ICMP rate limiting
   - Broadcast storm control
   - Resource limits

6. Logging, Monitoring, and Auditing (NIST SP 800-53 AU Family)
   - Centralized logging
   - Log integrity
   - NTP synchronization
   - SNMP monitoring security
   - NetFlow/sFlow analysis
   - Retention policies

7. Interface and Service Security (Attack Surface Reduction)
   - Unused interfaces shutdown
   - Unnecessary services disabled
   - Port security
   - DHCP snooping
   - Dynamic ARP Inspection

8. Management Plane Security (NIST Principles)
   - Out-of-band management
   - Management network isolation
   - Encrypted protocols only
   - Jump host/bastion architecture
   - Console security

9. Firmware and Configuration Management (NIST SP 800-53 CM Family)
   - Patch management
   - Configuration baselines
   - Change control
   - Vulnerability scanning
   - CIS benchmark compliance

10. NIST Compliance Mapping (CSF or SP 800-53)
    - Map controls to NIST framework
    - Identify compliance gaps
    - Document deviations
    - Provide remediation roadmap

Provide output in the following format:
- Executive summary with overall risk assessment
- Critical/High/Medium/Low findings with NIST references
- Compliance matrix
- Remediation roadmap with priorities
- Validation commands
```

### 3. Analyze Security Review Output

When the agent returns the review, organize by:
- **Critical Findings**: Immediate security risks requiring urgent action
- **High Findings**: Significant vulnerabilities to fix soon
- **Medium Findings**: Security improvements needed
- **Low Findings**: Best practice enhancements

### 4. Create Security Assessment Report

Structure the security findings in a comprehensive report:

**Executive Summary:**
- Overall Security Posture (Critical Risk / High Risk / Medium Risk / Low Risk)
- Number of findings by severity
- Top 5 critical security gaps
- NIST compliance status
- Key recommendations

**Risk Assessment Matrix:**
```
| Finding | Severity | NIST Control | Likelihood | Impact | Risk Score |
|---------|----------|--------------|------------|--------|------------|
| ...     | Critical | AC-2         | High       | High   | 9/10       |
```

**Detailed Findings:**
For each security issue:
```
[SEVERITY] Category: Finding Title

NIST Reference: SP 800-53 XX-Y / CSF Category

Location: <device/config section>

Vulnerability:
<Description of the security issue>

Risk:
<Potential impact and exploitation scenario>

Evidence:
<Configuration excerpt or observation>

Attack Vector:
<How this could be exploited>

Recommendation:
<Specific remediation steps with examples>

NIST Compliance:
<How remediation addresses NIST requirements>

Priority: [Immediate/30 days/90 days]
```

### 5. Map to NIST Cybersecurity Framework

Create CSF compliance matrix:

**Identify (ID):**
- Asset Management (ID.AM)
- Risk Assessment (ID.RA)
- Findings and gaps

**Protect (PR):**
- Access Control (PR.AC)
- Data Security (PR.DS)
- Findings and gaps

**Detect (DE):**
- Anomalies and Events (DE.AE)
- Continuous Monitoring (DE.CM)
- Findings and gaps

**Respond (RS):**
- Response Planning (RS.RP)
- Communications (RS.CO)
- Findings and gaps

**Recover (RC):**
- Recovery Planning (RC.RP)
- Improvements (RC.IM)
- Findings and gaps

### 6. Create Remediation Roadmap

Provide prioritized remediation plan:

**Phase 1: Immediate (0-30 days) - Critical Vulnerabilities**
1. Finding: Unencrypted management protocols
   - Action: Disable Telnet/HTTP, enable SSH/HTTPS only
   - Owner: Network Security Team
   - Effort: 2 days
   - NIST: SC-8, SC-13

2. Finding: No BGP authentication
   - Action: Implement MD5 authentication on all BGP sessions
   - Owner: Network Engineering
   - Effort: 1 week
   - NIST: SC-8

**Phase 2: Short-term (1-3 months) - High Priority**
[Continue with high priority items]

**Phase 3: Medium-term (3-6 months) - Medium Priority**
[Continue with medium priority items]

**Phase 4: Long-term (6-12 months) - Enhancements**
[Continue with improvements]

### 7. Provide Validation Commands

Include commands to verify security controls:

**Authentication and Access Control:**
```bash
# Verify SSH only (no Telnet)
show line vty 0 4 | include "transport input"
netstat -tuln | grep :23  # Should show no Telnet listener

# Check AAA configuration
show running-config | section aaa
show tacacs
show radius

# Verify session timeouts
show line vty 0 4 | include "exec-timeout"
```

**Encryption Verification:**
```bash
# Check routing protocol authentication
show running-config | section "router bgp"
show ip ospf interface | include "authentication"

# Verify VPN encryption
show crypto ipsec sa
show crypto isakmp sa

# Check SNMPv3
show snmp user
show running-config | include "snmp-server"
```

**Network Segmentation:**
```bash
# Verify VLAN configuration
show vlan brief
show vlan id 100

# Check ACLs
show ip access-lists
show running-config | include "access-list"

# Verify firewall rules (if applicable)
iptables -L -n -v
nft list ruleset
```

**Logging and Monitoring:**
```bash
# Check syslog configuration
show logging
show running-config | include logging

# Verify NTP
show ntp status
show ntp associations

# Check SNMP (should be v3 only)
show snmp community  # Should be empty or not exist
show snmp user
```

**Routing Security:**
```bash
# BGP prefix filtering
show ip bgp neighbors <peer> | include "prefix-list"
show ip prefix-list

# Route authentication
show ip bgp neighbors <peer> | include password
show ip ospf interface | include auth

# Check for bogon filtering
show ip access-lists | include "deny.*10\.0\.0\.0"
```

### 8. Document Compliance Mapping

Create NIST SP 800-53 compliance matrix:

| Control Family | Control | Status | Evidence | Gaps | Remediation |
|----------------|---------|--------|----------|------|-------------|
| AC (Access Control) | AC-2 Account Management | Partial | TACACS+ configured | No MFA | Implement MFA |
| AC-3 Access Enforcement | Non-Compliant | None | No ACLs | Implement ACLs |
| SC (System Communications) | SC-8 Transmission Confidentiality | Compliant | SSH only | - | - |
| SC-13 Cryptographic Protection | Partial | Strong ciphers | Weak BGP auth | Update to BGP-TCP-AO |
| AU (Audit and Accountability) | AU-2 Audit Events | Compliant | Syslog configured | - | - |
| AU-6 Audit Review | Partial | Logs collected | No analysis | Implement SIEM |

## Common Security Vulnerabilities

### Critical Security Issues

**Unencrypted Management Protocols:**
```
Vulnerability: Telnet, HTTP, SNMPv1/v2c in use
Risk: Credential theft, man-in-the-middle attacks
NIST: SC-8, SC-13
Fix: Use SSH, HTTPS, SNMPv3 exclusively
```

**Default or Weak Passwords:**
```
Vulnerability: Default credentials or weak passwords
Risk: Unauthorized access, privilege escalation
NIST: IA-5
Fix: Strong password policy, key-based authentication
```

**No Routing Protocol Authentication:**
```
Vulnerability: BGP/OSPF without authentication
Risk: Route hijacking, traffic interception
NIST: SC-8
Fix: Implement MD5 or stronger authentication
```

**No Network Segmentation:**
```
Vulnerability: Flat network design
Risk: Lateral movement, widespread compromise
NIST: SC-7
Fix: Implement VLANs, firewalls, ACLs
```

### High-Risk Security Issues

**Missing Logging and Monitoring:**
```
Vulnerability: Inadequate logging
Risk: Inability to detect/respond to incidents
NIST: AU-2, AU-6, SI-4
Fix: Centralized syslog, SNMP, NetFlow
```

**No Rate Limiting or DoS Protection:**
```
Vulnerability: No control plane protection
Risk: Denial of service attacks
NIST: SC-5
Fix: Implement CoPP, rate limiting
```

**Weak BGP Security:**
```
Vulnerability: No prefix filtering, no ROV
Risk: Route leaks, prefix hijacking
NIST: Best practices
Fix: Prefix lists, RPKI validation
```

## Security Best Practices

### Defense in Depth

**Layer 1: Physical Security**
- Console port security
- Secure rack access
- Environmental controls

**Layer 2: Network Segmentation**
- VLANs and VRFs
- Firewalls between zones
- DMZ for public services
- Micro-segmentation

**Layer 3: Access Control**
- Strong authentication (MFA)
- Least privilege access
- Role-based access control
- Regular access reviews

**Layer 4: Encryption**
- Management traffic encryption
- VPN for remote access
- Routing protocol authentication
- SNMPv3 only

**Layer 5: Monitoring and Detection**
- Centralized logging
- Anomaly detection
- Traffic analysis
- Security event correlation

**Layer 6: Incident Response**
- Response procedures
- Escalation paths
- Forensics capability
- Recovery procedures

### Security Hardening Checklist

- [ ] Disable Telnet, enable SSH only
- [ ] Disable HTTP, enable HTTPS only
- [ ] Use SNMPv3 with encryption
- [ ] Configure routing protocol authentication
- [ ] Implement BGP prefix filtering
- [ ] Configure control plane protection
- [ ] Enable logging to central server
- [ ] Configure NTP for time sync
- [ ] Shutdown unused interfaces
- [ ] Disable unnecessary services
- [ ] Implement AAA (TACACS+/RADIUS)
- [ ] Configure session timeouts
- [ ] Use strong passwords or keys
- [ ] Implement network segmentation
- [ ] Configure ACLs for traffic control
- [ ] Enable port security on switches
- [ ] Configure DHCP snooping
- [ ] Enable Dynamic ARP Inspection
- [ ] Implement management VLAN/VRF
- [ ] Regular firmware updates

## Notes

- Security is a continuous process, not a one-time check
- Risk tolerance varies by organization - work with security team
- Balance security with operational requirements
- Document all security decisions and accepted risks
- Revalidate security after any network changes
- Keep up with evolving threats and NIST updates
- Test security controls regularly

## Example Task Invocation

```
review-network-security Please review security of our data center network with 10 leaf-spine switches running BGP. We're preparing for SOC 2 audit and need NIST SP 800-53 compliance assessment. Main concerns are BGP security and encryption of management protocols. Configuration files attached.
```
