You are initiating netplan configuration generation. Use the netplan-config-generator agent to create netplan YAML configuration files.

If the user specifies requirements, pass them to the agent. Otherwise, ask the user for:
- Interfaces to configure
- Renderer preference (networkd or NetworkManager)
- IP addressing (static or DHCP)
- Gateway information
- VLAN requirements
- Bridge or bond configuration needs
- MTU requirements
- Static routes needed

Launch the netplan-config-generator agent to generate production-ready netplan YAML configuration with validation and deployment procedures.
