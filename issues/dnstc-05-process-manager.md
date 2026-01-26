# Process Manager

**Depends on:** #2 (Binary Manager)

## Summary

Manage lifecycle of gost and transport subprocesses (start, stop, reload, monitor).

**Motivation:** dnstc orchestrates multiple long-running processes. Need reliable process management with health monitoring and graceful shutdown.

## Design

**Managed processes:**
- gost (DNS proxy + transport load balancer)
- Slipstream client instance(s) - standalone mode
- DNSTT client instance(s)
- Shadowsocks client with Slipstream as SIP003 plugin

**Lifecycle operations:**
- Start process with config
- Stop process gracefully (SIGTERM, then SIGKILL)
- Reload config (SIGHUP or restart)
- Monitor process health (is it running?)

**Features:**
- Automatic restart on crash
- Log capture and forwarding
- Clean shutdown on dnstc exit
- Platform-appropriate signal handling (Windows vs Unix)
