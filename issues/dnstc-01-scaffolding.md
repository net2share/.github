# Project Scaffolding

## Summary

Set up the basic Go project structure for dnstc with CLI framework and shared library integration.

**Motivation:** Foundation for all other features. Consistent project structure with other net2share Go tools.

## Design

- Go module: `github.com/net2share/dnstc`
- CLI framework: [cobra](https://github.com/spf13/cobra)
- Shared library: [go-corelib](https://github.com/net2share/go-corelib) for OS detection and TUI
- Cross-platform support: Windows, macOS, Linux

### Interactive Menu

Run without arguments for interactive menu:
```bash
dnstc
```

Menu options (placeholder):
- Start tunnel
- Stop tunnel
- Status
- Configuration
- Exit

### CLI Commands

```bash
dnstc start       # Start tunnel service
dnstc stop        # Stop tunnel service
dnstc status      # Show current status
dnstc config      # Show/edit configuration
```

### Flags

Common flags across commands:
- `--config` - Path to config file
- `--verbose` - Enable verbose output
- `--help` - Show help

**Scope:** This issue is about creating the project structure, menu, and command skeleton only. Commands and menus should display dummy/placeholder text. No actual functionality - that comes in later issues.
