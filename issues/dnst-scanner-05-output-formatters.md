# Output Formatters

**Depends on:** #3 (Basic Scan), #4 (E2E Validation)

## Summary

Define output data structure and formats for scanner results. Referenced by #3 and #4 for implementation.

**Motivation:** Consistent output structure enables dnstc to parse results reliably. Multiple formats support different use cases (JSON for programmatic, plain text for human).

## Data Structure

```json
{
  "scan_metadata": {
    "timestamp": "2024-01-26T12:00:00Z",
    "total_input": 10000,
    "total_responding": 450,
    "tunnel_domain": "t.example.com",
    "e2e_enabled": true,
    "e2e_config": {
      "slipstream_health_domain": "hc-s.example.com",
      "dnstt_health_domain": "hc-d.example.com"
    }
  },
  "resolvers": [
    {
      "ip": "1.2.3.4",
      "ping_ms": 25,
      "dns_queries": {
        "google_com": {
          "success": true,
          "response_ms": 45,
          "result": "142.250.185.78"
        },
        "microsoft_com": {
          "success": true,
          "response_ms": 52,
          "result": "20.70.246.20"
        },
        "blocked_domain": {
          "domain": "facebook.com",
          "success": true,
          "response_ms": 38,
          "result": "157.240.1.35",
          "hijacked": false
        },
        "tunnel_domain": {
          "success": true,
          "response_ms": 120,
          "result": "ns.example.com"
        }
      },
      "classification": "clean",
      "e2e": {
        "slipstream": {
          "domain": "hc-s.example.com",
          "success": true,
          "latency_ms": 850
        },
        "dnstt": {
          "domain": "hc-d.example.com",
          "success": true,
          "latency_ms": 920
        }
      }
    },
    {
      "ip": "5.6.7.8",
      "ping_ms": 40,
      "dns_queries": {
        "google_com": { "success": true, "response_ms": 30, "result": "142.250.185.78" },
        "microsoft_com": { "success": true, "response_ms": 35, "result": "20.70.246.20" },
        "blocked_domain": {
          "domain": "facebook.com",
          "success": true,
          "response_ms": 15,
          "result": "10.10.34.35",
          "hijacked": true
        },
        "tunnel_domain": { "success": true, "response_ms": 95, "result": "ns.example.com" }
      },
      "classification": "censored",
      "e2e": {
        "slipstream": {
          "domain": "hc-s.example.com",
          "success": false,
          "error": "timeout"
        },
        "dnstt": {
          "domain": "hc-d.example.com",
          "success": false,
          "error": "timeout"
        }
      }
    }
  ]
}
```

## Output Formats

### JSON (default for programmatic use)

Full structure as shown above. Use `--format json`.

### Plain Text (human readable)

```
# dnst-scanner results - 2024-01-26T12:00:00Z
# Input: 10000, Responding: 450
# Tunnel domain: t.example.com
# E2E: slipstream=hc-s.example.com, dnstt=hc-d.example.com

# IP            Ping  Class     Tunnel  E2E-Slip  E2E-DNSTT
1.2.3.4         25ms  clean     ok      ok        ok
5.6.7.8         40ms  censored  ok      fail      fail
```

Use `--format plain`.

## CLI

```bash
# JSON to stdout (default)
dnst-scanner scan --tunnel-domain t.example.com --format json

# Plain text to file
dnst-scanner scan --tunnel-domain t.example.com --format plain --output results.txt

# JSON to file
dnst-scanner scan --tunnel-domain t.example.com --output results.json
```

## Field Definitions

| Field | Description |
|-------|-------------|
| `classification` | `clean` = resolves blocked domains properly, `censored` = hijacks to 10.x.x.x |
| `hijacked` | true if blocked domain resolved to 10.x.x.x range |
| `e2e.*.domain` | Health check domain used for the E2E test |
| `e2e.*.success` | true if tunnel connection succeeded |
| `e2e.*.latency_ms` | Round-trip time through tunnel |
| `e2e.*.error` | Error message if failed |
