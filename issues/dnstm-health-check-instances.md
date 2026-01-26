# Feature: Health Check Instances

**Depends on:** #26 (Multi-Instance Support with DNS Proxy)

## Summary

Add support for deploying dedicated health check instances for Slipstream and DNSTT transports. These lightweight instances are used by client-side `dnst-scanner` for end-to-end validation of DNS resolvers.

**Motivation:** Clients need to verify that a DNS resolver can actually establish a working tunnel, not just respond to DNS queries. Health check instances provide a known endpoint for this validation without interfering with production traffic.

## Design

### Health Check Instance
- Minimal transport instance with dedicated domain (e.g., `hc-s.example.com`)
- Responds to a simple health check protocol (e.g., echo back a token)
- Runs alongside production instances via multi-instance infrastructure
- Separate domain routing through DNS proxy

### Instance Types
- `slipstream-health`: Slipstream health check endpoint
- `dnstt-health`: DNSTT health check endpoint

### CLI
```bash
# Deploy health check instance
dnstm instance add slipstream --name health --domain hc.example.com --mode health-check

# Or dedicated command
dnstm health-check add slipstream --domain hc-s.example.com
dnstm health-check add dnstt --domain hc-d.example.com
```

### Client Integration
- `dnst-scanner` connects to health check domains during e2e validation phase
- Validates full tunnel path: client → resolver → server → response
