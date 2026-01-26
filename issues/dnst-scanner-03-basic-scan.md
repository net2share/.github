# Basic Scan (Step 1)

**Depends on:** #1 (Project Scaffolding), #2 (Resolver List Fetcher)

## Summary

Test resolver IPs with connectivity checks and multi-domain DNS queries to identify working resolvers and their behavior characteristics.

**Motivation:** First filtering step to reduce the large raw IP list (~10k+) to resolvers that respond and behave well. Different query types help identify resolvers suitable for DNS tunneling.

## Design

### Scan Steps

1. **Ping check**: Verify basic connectivity before DNS queries
2. **Normal domain queries**: Test basic DNS resolution
   - `google.com` - commonly whitelisted in Iran
   - `microsoft.com` - another baseline test
3. **Blocked domain query**: Test censorship behavior
   - `facebook.com` or `x.com`
   - Iranian resolvers often hijack and return `10.x.x.x` ranges
   - Resolvers that properly resolve blocked domains are often better for tunneling
4. **Custom tunnel domain query**: Test if resolver can reach our NS subdomain
   - Provided via `--tunnel-domain` flag
   - Tests if resolver can resolve the NS record used for tunneling

### Output

Filter out non-responding IPs and non-DNS servers. For each working resolver, include:

- IP address
- Response times for each query type
- Normal domain results (google.com, microsoft.com)
- Blocked domain result (actual IP vs hijacked 10.x.x.x)
- Tunnel domain resolution result (success/fail)
- Classification: `clean` (properly resolves blocked domains) vs `censored` (hijacks)

### Implementation Notes

- Concurrent workers with configurable parallelism (`--workers`)
- Configurable timeout per resolver (`--timeout`)
- Use Go's `net` package or [miekg/dns](https://github.com/miekg/dns) library

### Regarding NS vs A Record Testing

For basic scan, testing NS subdomain resolution is sufficient:
- NS subdomain (e.g., `t.example.com`) returns NS record pointing to `ns.example.com`
- This confirms resolver can reach our authoritative DNS
- Actual tunnel data flow testing happens in E2E validation (#4) via health check endpoints
