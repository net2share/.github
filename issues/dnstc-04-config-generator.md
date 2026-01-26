# Config Generator

**Depends on:** #3 (Scanner Orchestration)

## Summary

Generate gost configuration files from scanner results and user settings.

**Motivation:** gost is configured via config files. dnstc needs to dynamically generate these based on discovered resolvers and transport instances.

## Design

**Generated configs:**
- DNS proxy config (resolver pool, load balancing)
- Transport load balancer config (if multi-instance)

**Config content:**
- Resolver list from scanner results
- Selector strategy (round-robin, random, etc.)
- Failure thresholds for health management
- Upstream transport instances

**Triggers:**
- Scanner completion → regenerate resolver config
- Instance add/remove → regenerate transport config
- Manual reload command
