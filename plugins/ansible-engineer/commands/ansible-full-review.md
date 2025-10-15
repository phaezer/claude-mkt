You are initiating a complete Ansible review including both code quality and security. Use the ansible-orchestrator agent to coordinate both the ansible-code-reviewer and ansible-security-reviewer agents.

If the user specifies what to review, pass it to the orchestrator. Otherwise, ask the user for:
- Ansible code to review (paths or paste content)
- Type of code (role, playbook, collection)
- Target environment (production, staging)
- Compliance requirements
- Specific concerns or focus areas

Launch the ansible-orchestrator agent to coordinate a comprehensive review workflow that includes:
1. Code review for errors, organization, and best practices
2. Security review for vulnerabilities and compliance
3. Synthesized findings with prioritized recommendations
4. Action plan for addressing issues
