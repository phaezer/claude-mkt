You are initiating a new Ansible project structure. Use the ansible-developer agent to create a complete Ansible project with proper organization.

If the user specifies requirements, pass them to the agent. Otherwise, ask the user for:
- Project name and purpose
- Initial roles needed
- Inventory structure (environments: dev, staging, prod)
- Whether to include Ansible Vault setup
- Whether to include Molecule testing structure
- Any specific requirements or standards

Launch the ansible-developer agent to create a complete Ansible project structure including:
- Directory structure (roles, playbooks, inventory, group_vars, host_vars)
- ansible.cfg configuration
- .gitignore for Ansible projects
- README with project documentation
- Example playbooks and inventory
- Vault file structure (encrypted)
- Optional: Molecule test structure
