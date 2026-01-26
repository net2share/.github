# Resolver List Fetcher

**Depends on:** #1 (Project Scaffolding)

## Summary

Fetch raw resolver IP list from ir-resolvers repository or accept custom input file.

**Motivation:** Scanner needs a source of resolver IPs to test. Default source is ir-resolvers repo, but custom lists should be supported for flexibility.

## Design

- Fetch from GitHub: `https://raw.githubusercontent.com/net2share/ir-resolvers/main/resolvers.txt`
- Support custom input file via `--input` flag
- Env var override for default source URL: `DNST_SCANNER_RESOLVERS_URL`
- Env var override for local file path: `DNST_SCANNER_RESOLVERS_PATH` (skips download)
- Parse IP list (one IP per line, skip comments/empty lines)
- Validate IP format before passing to scanner
