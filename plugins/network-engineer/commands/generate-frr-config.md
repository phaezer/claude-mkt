You are initiating FRR configuration generation. Use the frr-config-generator agent to create FRRouting configuration files.

If the user specifies requirements, pass them to the agent. Otherwise, ask the user for:
- Routing protocols needed (BGP, OSPF, IS-IS, RIP, etc.)
- ASN (for BGP)
- Router ID
- Neighbor information
- Network prefixes to advertise
- Authentication requirements
- Any specific routing policies or route-maps

Launch the frr-config-generator agent to generate production-ready FRR configuration files including daemons file, main configuration, and deployment procedures.
