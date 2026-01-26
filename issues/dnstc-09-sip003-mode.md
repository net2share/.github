# SIP003 Plugin Mode

**Depends on:** #2 (Binary Manager), #5 (Process Manager)

## Summary

Run Shadowsocks client with Slipstream as SIP003 transport plugin.

**Motivation:** SIP003 is the standard plugin interface for Shadowsocks. Provides mature SOCKS5/HTTP proxy with encryption on top of DNS tunnel transport.

## Design

**Components:**
- shadowsocks-rust client (`sslocal`)
- Slipstream client as SIP003 plugin

**Configuration:**
- Shadowsocks server address/port/password/method
- Plugin: slipstream
- Plugin opts: domain, fingerprint, dns-server (from resolver pool)

**Data flow:**
```
App → sslocal (SOCKS5) → Slipstream Plugin → DNS Proxy (resolvers) → Server
```

**CLI:**
```bash
dnstc start --mode sip003 \
  --ss-server example.com:8388 \
  --ss-password secret \
  --domain t.example.com \
  --fingerprint abc123
```

**Binary management:**
- Download shadowsocks-rust sslocal binary
- Env var override: `DNSTC_SSLOCAL_PATH`
