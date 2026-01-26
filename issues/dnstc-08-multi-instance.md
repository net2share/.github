# Multi-Instance Transport Mode

**Depends on:** #7 (Single Transport Mode)

## Summary

Run multiple transport instances with gost load balancing for higher aggregate bandwidth.

**Motivation:** Single DNS tunnel has limited bandwidth. Multiple instances can aggregate throughput by distributing connections.

## Design

**Architecture:**
```
App → gost (load balancer) → Transport Instance 1 → DNS Proxy → Server
                           → Transport Instance 2 → DNS Proxy → Server
                           → Transport Instance N → DNS Proxy → Server
```

**gost features used:**
- [Selector](https://gost.run/en/concepts/selector/) - distribute connections across instances
- Failure detection - remove dead instances from pool

**Configuration:**
- Number of instances
- Can mix transport types (e.g., 2x Slipstream + 1x DNSTT)
- Per-instance server config if connecting to different servers

**CLI:**
```bash
dnstc start --instances 3 --transport slipstream --domain t.example.com
```
