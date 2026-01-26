# Single Transport Mode

**Depends on:** #5 (Process Manager)

## Summary

Configure and run a single Slipstream or DNSTT transport instance through gost DNS proxy.

**Motivation:** Basic use case - single tunnel connection. Foundation for multi-instance mode.

## Design

**Supported transports:**
- Slipstream client (standalone)
- DNSTT client (standalone)

**Configuration:**
- Transport type (slipstream/dnstt)
- Server domain
- Server credentials (TLS fingerprint / pubkey)
- Local listen address

**Data flow:**
```
App → gost (SOCKS/HTTP proxy) → Transport Client → DNS Proxy (resolvers) → Server
```

**CLI:**
```bash
dnstc start --transport slipstream --domain t.example.com --fingerprint abc123
```
