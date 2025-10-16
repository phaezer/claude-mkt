---
description: Design network architecture and topology
argument-hint: Optional network requirements
---

You are orchestrating a comprehensive network design workflow using a structured approach to create detailed network architecture plans.

## Workflow Steps

### 1. Gather Requirements

If the user provides specific requirements in their message, use those directly. Otherwise, ask the user for:

**Network Type and Purpose:**
- Network environment (data center, campus, branch office, cloud, hybrid)
- Primary use case (server connectivity, user access, WAN, internet edge)
- Criticality level (production, development, testing)

**Scale Requirements:**
- Number of devices to support (servers, users, IoT devices)
- Expected throughput (1G, 10G, 25G, 100G, 400G)
- Number of sites/locations
- Growth projections (1 year, 3 years, 5 years)

**Redundancy and High Availability:**
- Uptime requirements (99.9%, 99.99%, 99.999%)
- Acceptable downtime window
- Failure tolerance (single link, single device, entire site)
- Disaster recovery requirements
- Geographic redundancy needs

**Routing and Connectivity:**
- Routing protocol preferences (BGP, OSPF, IS-IS, static)
- Layer 2 vs Layer 3 architecture
- Network segmentation requirements
- Internet connectivity (single/dual provider, bandwidth)
- WAN connectivity requirements

**Security Requirements:**
- Compliance requirements (PCI-DSS, HIPAA, SOC 2, NIST)
- Network segmentation needs (DMZ, internal zones, guest)
- Firewall requirements
- IDS/IPS requirements
- Access control requirements

**Technical Constraints:**
- Existing infrastructure to integrate with
- Vendor preferences or restrictions
- Budget constraints
- Physical space constraints
- Power and cooling constraints

**Performance Requirements:**
- Latency requirements
- Packet loss tolerance
- QoS requirements
- Bandwidth guarantees

### 2. Launch network-orchestrator Agent

Use the Task tool to launch the network-orchestrator agent with a comprehensive design prompt:

```
Design a network architecture for the following requirements:

[Insert all gathered requirements here]

Please provide a comprehensive network design including:

1. Network Architecture Overview
   - Architecture type (spine-leaf, three-tier, collapsed core, etc.)
   - Design rationale and trade-offs
   - Scalability analysis

2. Topology Design
   - Physical topology diagram description
   - Logical topology diagram description
   - Device placement and roles
   - Link design and redundancy

3. IP Addressing Scheme
   - Subnet allocation plan
   - IP address management strategy
   - VLAN design and numbering
   - Loopback addressing scheme

4. Routing Design
   - Routing protocol selection and justification
   - IGP design (areas, levels, metrics)
   - BGP design (AS numbering, peering strategy)
   - Route summarization strategy
   - Failure scenario analysis

5. High Availability Design
   - Redundancy architecture
   - Failover mechanisms
   - Link aggregation design
   - Gateway redundancy (VRRP, HSRP, anycast)
   - Fast convergence mechanisms (BFD, tuned timers)

6. Security Architecture
   - Network segmentation design
   - Trust zones and boundaries
   - Firewall placement
   - ACL strategy
   - Management network design

7. Device Requirements
   - Switch/router specifications
   - Port count and speed requirements
   - Buffer and table size requirements
   - Feature requirements

8. Implementation Phases
   - Phase 1: Core infrastructure
   - Phase 2: Distribution/access layers
   - Phase 3: Services and optimization
   - Migration strategy (if applicable)

9. Validation and Testing Plan
   - Design validation steps
   - Testing scenarios
   - Acceptance criteria

10. Documentation Deliverables
    - Network diagrams
    - IP address inventory
    - Configuration templates
    - Runbooks
```

### 3. Review Design Output

When the agent returns the design, review it for:
- Completeness of all required sections
- Alignment with requirements
- Realistic scalability projections
- Appropriate technology selections
- Clear migration/implementation path
- Comprehensive failure scenario analysis

### 4. Create Design Documentation

Ensure the design includes comprehensive documentation:

**Network Diagrams:**
- Physical topology (rack elevations, cable paths)
- Logical topology (L2 and L3)
- IP addressing diagram
- Security zones diagram
- Failure scenario diagrams

**Design Specifications:**
- Bill of Materials (BOM)
- Cable schedule
- IP address allocation table
- VLAN table
- Routing protocol configuration summary

**Implementation Guide:**
- Pre-implementation checklist
- Step-by-step implementation procedure
- Configuration snippets
- Testing procedures
- Rollback procedures

### 5. Validate Design Against Requirements

Conduct a requirements validation check:

```
Requirements Validation Checklist:

□ Meets capacity requirements (current and future)
□ Meets redundancy/HA requirements
□ Meets performance requirements (latency, throughput)
□ Meets security requirements
□ Scalable to projected growth
□ Within budget constraints
□ Compatible with existing infrastructure
□ Follows industry best practices
□ Has documented failure scenarios
□ Includes clear implementation plan
```

### 6. Conduct Design Review Sessions

Organize design review with stakeholders:

**Pre-Review Preparation:**
- Distribute design documentation 1 week before review
- Prepare presentation slides
- Identify key decision points
- Prepare answers to anticipated questions

**Review Agenda:**
1. **Executive Summary** (15 min)
   - Business requirements recap
   - Proposed architecture overview
   - Key benefits and trade-offs

2. **Technical Deep Dive** (45 min)
   - Topology and architecture
   - IP addressing and routing
   - High availability design
   - Security architecture

3. **Implementation Plan** (30 min)
   - Phased approach
   - Timeline and milestones
   - Resource requirements
   - Risk mitigation

4. **Q&A and Feedback** (30 min)
   - Stakeholder questions
   - Feedback collection
   - Action items

### 7. Iterate Based on Feedback

After design review:
- Document all feedback and concerns
- Update design based on valid concerns
- Re-evaluate technology choices if needed
- Update cost estimates
- Revise implementation timeline
- Schedule follow-up review if major changes

### 8. Create Final Design Package

Assemble comprehensive design deliverables:

**Design Documents:**
1. **Executive Summary** (2-3 pages)
   - Business requirements
   - Proposed solution overview
   - Cost and timeline summary
   - Key benefits

2. **Architecture Design Document** (20-50 pages)
   - Detailed architecture description
   - Technology selection rationale
   - All network diagrams
   - IP addressing tables
   - Device specifications
   - Security design

3. **Implementation Plan** (10-20 pages)
   - Phased implementation approach
   - Detailed task list with owners
   - Timeline/Gantt chart
   - Testing plan
   - Risk assessment and mitigation

4. **Configuration Templates**
   - Base configuration templates
   - Security hardening templates
   - Monitoring configuration

5. **Operations Runbook**
   - Day 1 operations procedures
   - Troubleshooting guides
   - Escalation procedures
   - Maintenance procedures

## Best Practices for Network Design

### Architecture Selection

**Spine-Leaf (Clos) Architecture:**
- **Use for**: Data centers, high-performance computing
- **Benefits**: Predictable latency, easy scaling, high bandwidth
- **Considerations**: Requires L3 everywhere, more complex routing

**Three-Tier (Core-Distribution-Access):**
- **Use for**: Campus networks, traditional enterprise
- **Benefits**: Well understood, hierarchical, scalable
- **Considerations**: Can have bottlenecks at aggregation layer

**Collapsed Core (Two-Tier):**
- **Use for**: Small to medium enterprises, branch offices
- **Benefits**: Simplified, lower cost, easier management
- **Considerations**: Less scalable, potential bottlenecks

### IP Addressing Best Practices

1. **Use RFC1918 Private Address Space Efficiently**
   - 10.0.0.0/8 for large enterprises
   - 172.16.0.0/12 for medium enterprises
   - 192.168.0.0/16 for small offices

2. **Allocate Contiguous Blocks**
   - Allow for route summarization
   - Simplify routing tables
   - Enable easier growth

3. **Reserve Ranges**
   - Management network: /24 per location
   - Loopbacks: /32 from dedicated range
   - Point-to-point links: /30 or /31
   - Future growth: 30-50% headroom

4. **Document Everything**
   - IPAM (IP Address Management) system
   - Spreadsheet with allocations
   - DNS and DHCP integration

### Routing Protocol Selection

**BGP:**
- **Use for**: Data center fabrics, internet edge, multi-tenant
- **Pros**: Scalable, flexible policy control, industry standard
- **Cons**: More complex, requires careful design

**OSPF:**
- **Use for**: Campus networks, enterprise core
- **Pros**: Fast convergence, well understood, feature-rich
- **Cons**: Flat area design doesn't scale, CPU intensive

**IS-IS:**
- **Use for**: Service provider networks, very large enterprises
- **Pros**: Scales well, stable, low overhead
- **Cons**: Less common, fewer engineers familiar with it

**Static Routes:**
- **Use for**: Small networks, specific use cases, backup paths
- **Pros**: Simple, predictable, no protocol overhead
- **Cons**: Not scalable, manual updates, no automatic failover

### High Availability Design Principles

1. **Eliminate Single Points of Failure**
   - Redundant power supplies
   - Dual network paths
   - Multiple uplinks
   - Redundant services (DNS, DHCP, etc.)

2. **Use Redundancy Protocols**
   - VRRP/HSRP for gateway redundancy
   - LACP for link aggregation
   - BFD for fast failure detection
   - Route redundancy with equal-cost multipath

3. **Design for Fast Convergence**
   - Tune protocol timers appropriately
   - Use BFD (sub-second detection)
   - Pre-provision backup paths
   - Minimize spanning-tree domains

4. **Consider Failure Scenarios**
   - Single link failure
   - Single device failure
   - Power failure
   - Site failure (for multi-site)
   - Human error

### Security Design Principles

1. **Defense in Depth**
   - Multiple layers of security controls
   - Network segmentation
   - Least privilege access
   - Monitoring and logging

2. **Network Segmentation**
   - Separate trust zones (internet, DMZ, internal, management)
   - VLANs for logical separation
   - Firewalls between zones
   - Micro-segmentation for critical assets

3. **Access Control**
   - Management network isolation
   - SSH key authentication
   - TACACS+ or RADIUS
   - Role-based access control

4. **Monitoring and Logging**
   - Centralized syslog
   - SNMP monitoring
   - NetFlow/sFlow for traffic analysis
   - Security event correlation

## Common Design Patterns

### Data Center Leaf-Spine

**Architecture:**
- Spine layer: High-capacity switches (100G/400G)
- Leaf layer: ToR switches connecting servers
- Every leaf connects to every spine
- L3 routing to the leaf switches

**Routing:**
- BGP for underlay (eBGP with unique ASN per leaf)
- EVPN for overlay
- BFD for fast convergence

**Benefits:**
- Linear scaling
- Predictable latency
- High bandwidth
- Easy to automate

### Campus Three-Tier

**Architecture:**
- Core: High-speed backbone (collapsed to 2+ switches)
- Distribution: Aggregates access switches, L3 boundary
- Access: User/device connectivity, L2 usually

**Routing:**
- OSPF for campus routing
- Default gateway at distribution
- Static routes to core (optional)

**Benefits:**
- Well understood design
- Clear hierarchy
- Scalable for medium to large campuses

### Branch Office Hub-and-Spoke

**Architecture:**
- Central hub (data center or headquarters)
- Branch sites connect to hub
- Optional branch-to-branch (full mesh) for critical sites

**Connectivity:**
- MPLS WAN or SD-WAN
- Internet VPN backup
- Dual-homed branches for critical sites

**Routing:**
- BGP for WAN (with MPLS provider)
- OSPF or EIGRP internally
- Default route to hub

## Design Validation Checklist

Before finalizing design:

**Capacity Planning:**
- [ ] Port density meets current needs + 30% growth
- [ ] Link bandwidth supports peak traffic + 50% headroom
- [ ] Routing table size within device limits
- [ ] MAC table size sufficient for L2 domains

**Redundancy:**
- [ ] No single points of failure in critical paths
- [ ] All uplinks are redundant
- [ ] Power is redundant
- [ ] Management access is redundant

**Performance:**
- [ ] Latency meets requirements
- [ ] Bandwidth meets requirements
- [ ] QoS design supports critical applications
- [ ] Convergence time is acceptable

**Security:**
- [ ] Network segmentation implemented
- [ ] Firewalls properly placed
- [ ] ACLs defined for critical segments
- [ ] Management network isolated
- [ ] Monitoring and logging configured

**Scalability:**
- [ ] Design supports 3-5 year growth
- [ ] IP addressing allows for expansion
- [ ] Routing design scales appropriately
- [ ] Physical space for additional devices

**Operational:**
- [ ] Monitoring and management tools identified
- [ ] Automation approach defined
- [ ] Documentation complete
- [ ] Training plan for operations team
- [ ] Runbooks created

## Notes

- Network design is iterative - expect multiple revision cycles
- Involve all stakeholders early (network, security, operations, business)
- Consider operational complexity vs. technical perfection
- Document design decisions and trade-offs
- Plan for day 2 operations from the start
- Always have a rollback plan
- Test designs in lab before production deployment

## Example Task Invocation

```
design-network I need to design a data center network for 500 servers across 10 racks, requiring 25G server connectivity and 100G spine uplinks, with full redundancy and BGP routing for a multi-tenant cloud environment
```
