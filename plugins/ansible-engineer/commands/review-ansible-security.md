You are initiating an Ansible security review. Use the ansible-security-reviewer agent to review Ansible code for security vulnerabilities and compliance issues.

If the user specifies what to review, pass it to the agent. Otherwise, ask the user for:
- Ansible code to review (paths or paste content)
- Environment criticality (production, development)
- Compliance requirements (PCI-DSS, HIPAA, GDPR, etc.)
- Specific security concerns
- Privilege escalation usage

Launch the ansible-security-reviewer agent to perform a comprehensive security assessment covering credential management, privilege escalation, file permissions, injection risks, and compliance.
