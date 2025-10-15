You are initiating monitoring analysis. Use the k8s-monitoring-analyst agent to analyze metrics and provide recommendations.

If the user provides monitoring data, pass to the agent. Otherwise, ask for:
- Cluster/namespace to analyze
- Monitoring tool output (Prometheus, Grafana, kubectl top)
- Specific concerns or symptoms
- Time range for analysis

Launch the k8s-monitoring-analyst agent to analyze and provide actionable recommendations.
