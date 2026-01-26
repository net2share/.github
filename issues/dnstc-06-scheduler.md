# Scheduler

**Depends on:** #3 (Scanner Orchestration), #4 (Config Generator), #5 (Process Manager)

## Summary

Schedule periodic scanner runs and handle config updates with process reloads.

**Motivation:** Resolver health changes over time. Regular scanning keeps the pool fresh. Automated scheduling removes manual intervention.

## Design

**Scheduled tasks:**
- Scanner runs (configurable interval, e.g., every 30 minutes)
- Resolver health checks (more frequent, e.g., every 5 minutes)

**Workflow:**
1. Scheduler triggers scanner run
2. Scanner completes → new resolver list
3. Config generator updates gost config
4. Process manager reloads gost

**Configuration:**
- `--scan-interval` - time between full scans
- `--health-interval` - time between health checks
- Support for manual trigger via CLI command

**Features:**
- Randomized jitter to avoid thundering herd
- Skip if previous run still in progress
- Configurable retry on failure
