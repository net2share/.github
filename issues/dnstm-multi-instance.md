# Feature: Multi-Instance Support with DNS Proxy

## Summary

Add support for running multiple transport instances simultaneously, each handling different domain sets, with a local DNS proxy for domain-based routing.

**Motivation:** Currently dnstm only supports one active provider instance at a time. To scale and support different use cases (production tunnels, health checks, different backend targets), we need multiple instances running concurrently with intelligent DNS routing.

## Design Proposal

### DNS Proxy

- Local DNS proxy listening on port 53
- Routes incoming queries to appropriate transport instance based on domain
- NAT redirects external port 53 to the proxy

**Recommended:** [gost](https://github.com/go-gost/gost) - Go Simple Tunnel

gost is recommended because it covers both server-side (dnstm) and client-side (dnstc) DNS proxy needs with a single solution. Written in Go, cross-platform, actively maintained.

**gost features for this use case:**
- [DNS Proxy](https://gost.run/en/tutorials/dns/) - Listen for DNS queries, forward to upstream instances
- [Bypass](https://gost.run/en/concepts/bypass/) - Domain-based routing with exact match (`t1.example.com`) and wildcards (`*.example.com`)
- [Selector](https://gost.run/en/concepts/selector/) - Load balancing strategies if needed (round-robin, random, hash)

**Deployment model:**
- gost runs as standalone subprocess managed by dnstm
- dnstm generates gost config based on instance configuration
- dnstm starts/stops/reloads gost when instances change
- gost binary downloaded or provided via env var path override

**Alternatives:**
- [CoreDNS](https://coredns.io/) - CNCF graduated, plugin-based
- [dnsdist](https://dnsdist.org/) - PowerDNS load balancer with Lua scripting

### Instance Configuration

- Each instance is assigned a unique label/name during setup
- Domain-to-instance mapping stored in central config file (e.g., `/etc/dnstm/instances.conf`)
- Config tracks which domains/subdomains belong to each service
- DNS proxy reads this config to route queries

### Instance Properties

Each instance has its own:

- Label/name (user-defined identifier)
- Domain set (one or more domains mapped to this instance)
- Target address/port
- TLS cert or keys
- Systemd service (e.g., `slipstream-server@instance1.service`)

### Example Architecture

```
Port 53 → DNS Proxy → domain1.example.com → Slipstream Instance 1 (port 5301) → SSH
                    → domain2.example.com → Slipstream Instance 2 (port 5302) → SOCKS
                    → domain3.example.com → DNSTT Instance (port 5300) → custom target
```

### CLI

```bash
# Add new instance
dnstm instance add slipstream --name prod1 --domain t1.example.com --mode ssh

# List instances
dnstm instance list

# Remove instance
dnstm instance remove prod1
```
