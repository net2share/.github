# Project Scaffolding

## Summary

Set up the basic Go project structure for dnst-scanner with CLI framework and shared library integration.

**Motivation:** Foundation for all other features. Consistent project structure with other net2share Go tools.

## Design

- Go module: `github.com/net2share/dnst-scanner`
- CLI framework: [cobra](https://github.com/spf13/cobra)
- Shared library: [go-corelib](https://github.com/net2share/go-corelib) for OS detection and TUI
- Main command: `dnst-scanner scan`
- Config via flags and/or config file

**Scope:** This issue is about creating the project structure, menu, and command skeleton only. Commands should display dummy/placeholder text. No actual scanning functionality - that comes in later issues.
