# Observability

## Purpose

Observability provides visibility into the health and behavior of homelab infrastructure and services.

Monitoring should help answer:

- Is the system available?
- Is performance degrading?
- Are resources becoming constrained?
- What changed before a failure?
- Which layer is responsible for the problem?

## Monitoring Layers

Useful observability spans several layers:

```text
Hardware
   ↓
Hypervisor
   ↓
Operating System
   ↓
Container Runtime
   ↓
Application
   ↓
User-Facing Service
```

No single tool necessarily provides complete visibility across every layer.

## Infrastructure Monitoring

Virtualization and host-monitoring tools can provide information about:

- CPU utilization
- Memory utilization
- Storage
- VM state
- Host availability
- Resource pressure

## Metrics

Metrics platforms such as Prometheus can collect time-series measurements from infrastructure and applications.

## Visualization

Grafana can provide dashboards and visualization when a dedicated metrics stack is appropriate.

Monitoring tooling should be added because it answers an operational question, not simply because another dashboard is available.

## Security Monitoring

Security telemetry is a separate but complementary concern.

Wazuh provides security-oriented visibility while infrastructure monitoring focuses on health and performance.

## Alerting

Useful alerts should indicate conditions that require attention.

Excessive low-value alerts create noise and reduce confidence in the monitoring system.

## Documentation Boundary

Dashboards and examples should avoid exposing sensitive infrastructure details when screenshots or configurations are committed.

## Related Documentation

- Services/Wazuh.md
- Services/Pulse and ProxMenux.md
- Architecture/Homelab Architecture.md
