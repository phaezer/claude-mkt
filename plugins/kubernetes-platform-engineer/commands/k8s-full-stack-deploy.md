You are initiating orchestrated full-stack deployment. Use the k8s-orchestrator agent.

If the user specifies the stack, pass to the orchestrator. Otherwise, ask for:
- Application components
- Dependencies (databases, caches, etc.)
- Target environment
- Security requirements
- Monitoring needs
- GitOps automation preference

Launch the k8s-orchestrator agent to coordinate end-to-end deployment workflow including:
1. Configuration generation
2. Security review
3. Deployment
4. Monitoring setup
5. GitOps automation
