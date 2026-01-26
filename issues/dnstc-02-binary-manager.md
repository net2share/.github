# Binary Manager

**Depends on:** #1 (Project Scaffolding)

## Summary

Download and manage external binaries (gost, slipstream, dnstt, dnst-scanner) with support for env var path overrides.

**Motivation:** dnstc orchestrates multiple external tools. Need reliable way to download, verify, and update binaries across platforms.

## Design

**Managed binaries:**
- gost (DNS proxy, load balancer)
- slipstream-client
- dnstt-client
- dnst-scanner

**Download sources:**
- GitHub releases for each project
- Platform-specific binaries (windows/darwin/linux, amd64/arm64)

**Env var overrides:**
- `DNSTC_GOST_PATH` - path to gost binary
- `DNSTC_SLIPSTREAM_PATH` - path to slipstream-client
- `DNSTC_DNSTT_PATH` - path to dnstt-client
- `DNSTC_SCANNER_PATH` - path to dnst-scanner

**Features:**
- Version checking and updates
- Binary verification (checksums)
- Storage in platform-appropriate location
