# Net2Share

We are a group of independent developers working to bring light to Iran and help people stay connected during the ongoing digital blackout. **You are not alone!**

While our current focus is on supporting the people of Iran, Net2Share is dedicated to enabling internet access for users in any highly restricted and censored networks around the world.

## Current Focus

At the moment, DNS-based tunnels are among the few methods that remain effective in Iran, so this is where we're concentrating our efforts.

## Architecture Overview

### How DNS Tunneling Works

DNS tunneling encapsulates data within DNS queries and responses, allowing traffic to pass through networks that might otherwise block it. The client sends data encoded in DNS queries to a recursive resolver, which forwards them to an authoritative DNS server under our control, where the tunnel server decodes the traffic and routes it to the open internet.

```
┌──────────────────────────────────────────────────────────────────────────┐
│                         Restricted Network (Iran)                        │
│                                                                          │
│   ┌────────────┐      DNS Queries       ┌───────────────────┐            │
│   │   Client   │ ─────────────────────► │ Recursive DNS     │            │
│   │(dnstclient)│ ◄───────────────────── │ Resolver (Iran)   │            │
│   └────────────┘      DNS Responses     └─────────┬─────────┘            │
│                                                   │                      │
└───────────────────────────────────────────────────┼──────────────────────┘
                                                    │
                                          DNS Queries (forwarded)
                                                    │
                                                    ▼
┌──────────────────────────────────────────────────────────────────────────┐
│                             Open Internet                                │
│                                                                          │
│                      ┌───────────────────────────┐                       │
│                      │ Authoritative DNS Server  │                       │
│                      │ + Tunnel Server (dnstm)   │                       │
│                      └─────────────┬─────────────┘                       │
│                                    │                                     │
│                                    ▼                                     │
│                           ┌───────────────┐                              │
│                           │ Open Internet │                              │
│                           └───────────────┘                              │
└──────────────────────────────────────────────────────────────────────────┘
```

## Our Projects

### Core Tools

#### [DNS Tunnel Manager (dnstm)](https://github.com/net2share/dnstm)

A comprehensive tool for deploying and managing DNS tunnel server infrastructure on Linux.

- Install and configure DNS tunnel transports (Slipstream, DNSTT)
- Deploy Slipstream as a Shadowsocks SIP003 plugin with Shadowsocks server or sing-box core
- Set up SOCKS proxy using microsocks
- Manage users and hardened policies for SSH tunnels

#### [DNS Tunnel Client (dnstclient)](https://github.com/net2share/dnstclient)

A cross-platform client tool for connecting to DNS tunnel servers from restricted networks. Supports Windows, macOS, and Linux — _coming soon._

- Download and configure Slipstream and DNSTT in standalone mode
- Install Slipstream as a Shadowsocks SIP003 plugin with Shadowsocks client or sing-box
- Discover working recursive DNS resolver IPs using dnst-resolver-scanner
- Run a local DNS proxy with load balancing across multiple resolvers
- Orchestrate the entire flow between scanner, DNS proxy, and transport

#### [Slipstream Shadowsocks Android Plugin](https://github.com/net2share/slipstream-plugin-android)

A fork of the upstream [slipstream-plugin-android](https://github.com/Mygod/slipstream-plugin-android) project. The goal is to bring similar functionality to dnstclient on Android, including DNS resolver scanning and seamless integration with the Shadowsocks app.

#### [Iran Resolvers (ir-resolvers)](https://github.com/net2share/ir-resolvers)

A curated list of potential recursive DNS server IP addresses available within Iran's intranet (Filternet). dnst-resolver-scanner fetches its initial raw list from this repository.

#### [SSH Tunnel User Manager (sshtun-user)](https://github.com/net2share/sshtun-user)

A security-focused utility for creating and managing restricted SSH users on Linux servers. These users can only create SSH tunnels (SOCKS proxies and local port forwarding) with absolutely no shell access or system privileges. It configures hardened SSH settings, integrates fail2ban for brute-force protection, and supports both password and public key authentication—perfect for securely sharing tunnel access with others. This tool is integrated into dnstm for managing tunnel users.

#### [go-corelib](https://github.com/net2share/go-corelib)

Our shared Go library that powers the CLI tools above. It provides OS detection, automatic package manager identification, and beautiful terminal output with consistent styling. Used by dnstm, dnstclient, and sshtun-user. Any component or logic shared across multiple projects is abstracted and centralized here.

### DNS Tunnel Implementations

#### [slipstream-rust-build](https://github.com/net2share/slipstream-rust-build)

An automated mirror of the upstream Slipstream Rust project that continuously syncs and builds fresh binaries via GitHub Actions. Whenever the upstream project updates, this repository automatically compiles and releases binaries for x86_64 and ARM64 Linux—so you always have access to the latest version without needing to build from source. dnstm fetches the latest binaries directly from this project's release artifacts.

#### [dnstt](https://github.com/net2share/dnstt)

A mirror of the original DNSTT project with pre-compiled binaries available in GitHub Releases. DNSTT is a userspace DNS tunnel that enables secure, covert communication through DNS queries using DoH (DNS over HTTPS), DoT (DNS over TLS), and standard UDP DNS. It uses Noise protocol encryption for end-to-end security, ensuring your traffic remains private even from intermediate DNS resolvers. dnstm fetches the latest binaries directly from this project's release artifacts.

#### [dnstt-revived](https://github.com/net2share/dnstt-revived)

A fork of DNSTT with improvements tailored for the current Iranian network conditions. This project is currently in the R&D phase with some promising results, aiming to extend the original implementation while preserving its strong cryptographic guarantees.

### Utilities

#### [DNS Tunnel Resolver Scanner (dnst-resolver-scanner)](https://github.com/net2share/dnst-resolver-scanner)

A work-in-progress tool designed to scan and identify recursive DNS servers in Iran that are compatible with DNS tunneling. The goal is to provide an end-to-end solution for finding working resolver IPs that can be used to establish DNS tunnels like DNSTT and Slipstream.

#### [microsocks-build](https://github.com/net2share/microsocks-build)

Automated builds of the lightweight microsocks SOCKS5 proxy server. This repository monitors the upstream project daily and produces statically-linked binaries for multiple architectures (x86_64, i686, ARM64, ARM) using musl libc—resulting in portable executables that work across different Linux systems without dependency headaches. dnstm fetches the latest binaries directly from this project's release artifacts.

## Technical Diagrams

### Server-Side Architecture

The server runs outside the restricted network and handles incoming tunnel connections.

```mermaid
flowchart TB
    subgraph dnstm["dnstm (Server)"]
        direction TB
        A[Install & Configure]
    end

    subgraph transports["DNS Tunnel Transports"]
        B[Slipstream]
        C[DNSTT]
    end

    subgraph modes["Connection Modes"]
        D[Standalone Mode]
        E[Shadowsocks SIP003 Plugin]
    end

    subgraph shadowsocks["Shadowsocks Stack"]
        F[Shadowsocks Server]
        G[sing-box Core]
    end

    subgraph usermgmt["User Management"]
        H[sshtun-user]
        I[microsocks]
    end

    subgraph artifacts["Binary Sources"]
        J[slipstream-rust-build]
        K[dnstt releases]
        L[microsocks-build]
    end

    dnstm --> transports
    transports --> modes
    E --> shadowsocks
    dnstm --> usermgmt

    J -.->|fetches binaries| B
    K -.->|fetches binaries| C
    L -.->|fetches binaries| I
```

### Client-Side Architecture

The client runs inside the restricted network and orchestrates the connection flow.

```mermaid
flowchart TB
    subgraph resolver_discovery["Resolver Discovery"]
        A[ir-resolvers]
        B[dnst-resolver-scanner]
    end

    subgraph dns_proxy["Local DNS Proxy"]
        C[Load Balancer]
        D[Multiple Working Resolvers]
    end

    subgraph client["dnstclient"]
        E[Orchestrator]
    end

    subgraph transports["Transport Options"]
        F[Slipstream Standalone]
        G[DNSTT Standalone]
        H[Slipstream + Shadowsocks]
    end

    subgraph apps["Applications"]
        I[Browser / Apps]
    end

    A -->|raw IP list| B
    B -->|working resolvers| C
    C --> D
    E --> resolver_discovery
    E --> dns_proxy
    E --> transports
    transports -->|tunnel via| D
    I -->|traffic| transports
```

### Project Dependencies

How our repositories relate to each other:

```mermaid
flowchart LR
    subgraph core["Core Libraries"]
        A[go-corelib]
    end

    subgraph tools["Management Tools"]
        B[dnstm]
        B2[dnstclient]
        C[sshtun-user]
        D[dnst-resolver-scanner]
    end

    subgraph data["Data Sources"]
        E[ir-resolvers]
    end

    subgraph builds["Binary Builds"]
        F[slipstream-rust-build]
        G[dnstt]
        H[microsocks-build]
    end

    subgraph mobile["Mobile"]
        I[slipstream-plugin-android]
    end

    A --> B
    A --> B2
    A --> C
    C --> B
    E --> D
    D --> B2
    F --> B
    F --> B2
    G --> B
    G --> B2
    H --> B
```

## Contributing

Contributions are not just welcomed—they are **urgently needed** by our team and the people in Iran who depend on these tools.

### How You Can Help

- **Explore the projects**: Check all the repositories listed above, review open issues, pull requests, and discussions
- **Build and test**: Clone the projects, build them locally, and help identify bugs or edge cases
- **Report issues**: Create new issues for bugs you encounter, feature requests, or new ideas
- **Code contributions**: Pick up open issues and help with design and implementation, or review open PRs and provide feedback
- **Documentation**: Help improve documentation, write guides, or translate content

### A Note on Code Quality

We are heavily utilizing AI agents to accelerate development. While all code is reviewed and refactored before merging, the codebase may not be perfect. Pull requests for cleanup, refactoring, or improving code quality are greatly appreciated.

### Staying Anonymous

If you need to protect your identity while contributing, we've prepared a detailed guide on setting up a separate GitHub account, SSH keys, and git configuration to ensure your contributions remain anonymous.

**[Read the full guide: Staying Anonymous While Contributing](https://github.com/net2share/.github/blob/main/STAYING-ANONYMOUS.md)**
