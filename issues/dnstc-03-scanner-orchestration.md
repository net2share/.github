# Scanner Orchestration

**Depends on:** #2 (Binary Manager)

## Summary

Run dnst-scanner as subprocess, provide configuration, and process output to update resolver pool.

**Motivation:** Scanner discovers working resolvers. dnstc needs to orchestrate scanner runs and consume results to keep resolver pool updated.

## Design

**Scanner control:**
- Start dnst-scanner as subprocess
- Pass configuration via CLI flags
- Capture stdout/stderr
- Parse JSON output for resolver list

**Configuration passed to scanner:**
- Health check domains (from dnstc config)
- Worker count and timeout
- Input source (ir-resolvers or custom)

**Output handling:**
- Parse JSON results
- Update internal resolver pool
- Trigger DNS proxy config reload
