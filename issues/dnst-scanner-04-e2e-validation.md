# E2E Validation (Step 2) - Optional

**Depends on:** #3 (Basic Scan)

## Summary

Optional step that tests resolvers from basic scan with actual Slipstream/DNSTT tunnel connections via health check endpoints.

**Motivation:** Basic scan confirms DNS resolution works, but some resolvers may still fail with actual tunnel traffic due to rate limiting, DPI, or connection issues. E2E validation confirms the full tunnel path works.

## Design

### Prerequisites

- Slipstream and/or DNSTT client binaries available
- Server-side health check endpoints deployed (dnstm #27)
- Output from basic scan (list of working resolvers)

### How It Works

1. Takes resolver list from basic scan output
2. For each resolver, attempts actual tunnel connection:
   - Start Slipstream/DNSTT client using the resolver
   - Connect to health check endpoint on server
   - Verify response (e.g., echo back a token)
3. Mark resolvers as `e2e_slipstream: true/false`, `e2e_dnstt: true/false`

### CLI

```bash
# Basic scan only (default)
dnst-scanner scan --tunnel-domain t.example.com

# Basic scan + E2E validation
dnst-scanner scan --tunnel-domain t.example.com --e2e \
  --slipstream-health hc-s.example.com \
  --slipstream-fingerprint abc123 \
  --dnstt-health hc-d.example.com \
  --dnstt-pubkey xyz789
```

### Binary Management

- Slipstream/DNSTT binaries required for E2E
- Env var overrides: `DNST_SCANNER_SLIPSTREAM_PATH`, `DNST_SCANNER_DNSTT_PATH`
- Or download from GitHub releases if not provided

### Output

Extends basic scan output with E2E results. See #5 (Output Formatters) for complete data structure.

Key fields added per resolver:
- `e2e.slipstream.domain`: health check domain used
- `e2e.slipstream.success`: true/false
- `e2e.slipstream.latency_ms`: tunnel round-trip time
- `e2e.dnstt.*`: same structure for DNSTT
