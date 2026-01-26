# Net2Share

We are a group of independent developers working to bring light to Iran and help people stay connected during the ongoing digital blackout. **You are not alone!** 🫂

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
│   │  (dnstc)   │ ◄───────────────────── │ Resolver (Iran)   │            │
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
- Deploy Slipstream as a Shadowsocks SIP003 plugin with Shadowsocks server
- Run multiple transport instances with different domain sets, load balanced via a local DNS proxy. Each instance can handle different traffic types (VLESS+WS, SOCKS, etc.) with multi-domain support
- Custom target port configuration for standalone transport modes
- Set up health check instances with multi-domain support for Slipstream and DNSTT, used by client-side dnst-resolver-scanner for end-to-end validation, with domain-based routing via the DNS proxy
- Deploy Telegram MTProxy behind DNS tunnels, allowing Telegram users to connect using native proxy settings without additional client software
- Set up SOCKS proxy using microsocks
- Manage users and hardened policies for SSH tunnels

#### [DNS Tunnel Client (dnstc)](https://github.com/net2share/dnstc)

A cross-platform client tool for connecting to DNS tunnel servers from restricted networks. Supports Windows, macOS, and Linux — _coming soon._

- Download and configure Slipstream and DNSTT in standalone mode
- Install Slipstream as a Shadowsocks SIP003 plugin with Shadowsocks client
- Discover working recursive DNS resolver IPs using dnst-resolver-scanner
- Continuously monitor resolver health and maintain a pool of working resolvers in the DNS proxy
- Run a local DNS proxy with load balancing across multiple resolvers
- Run multiple transport instances with load balancing across them for higher aggregate bandwidth
- Orchestrate the entire flow between scanner, DNS proxy, and transports

#### [DNS Tunnel Resolver Scanner (dnst-resolver-scanner)](https://github.com/net2share/dnst-resolver-scanner)

A tool designed to scan and identify recursive DNS servers in Iran that are compatible with DNS tunneling. Provides an end-to-end solution for finding working resolver IPs that can be used to establish DNS tunnels like DNSTT and Slipstream.

- Two-step scanning: first scan the raw resolver list to find working recursive resolvers
- End-to-end validation: test working resolvers with actual transport protocols (Slipstream, DNSTT) to verify they can establish real tunnels

#### [Iran Resolvers (ir-resolvers)](https://github.com/net2share/ir-resolvers)

A curated list of potential recursive DNS server IP addresses available within Iran's intranet (Filternet). dnst-resolver-scanner fetches its initial raw list from this repository.

#### [Slipstream Shadowsocks Android Plugin](https://github.com/net2share/slipstream-plugin-android)

A fork of the upstream [slipstream-plugin-android](https://github.com/Mygod/slipstream-plugin-android) project. The goal is to bring similar functionality to dnstc on Android, including DNS resolver scanning and seamless integration with the Shadowsocks app.

#### [SSH Tunnel User Manager (sshtun-user)](https://github.com/net2share/sshtun-user)

A security-focused utility for creating and managing restricted SSH users on Linux servers. These users can only create SSH tunnels (SOCKS proxies and local port forwarding) with absolutely no shell access or system privileges. It configures hardened SSH settings, integrates fail2ban for brute-force protection, and supports both password and public key authentication—perfect for securely sharing tunnel access with others. This tool is integrated into dnstm for managing tunnel users.

#### [go-corelib](https://github.com/net2share/go-corelib)

Our shared Go library that powers the CLI tools above. It provides OS detection, automatic package manager identification, and beautiful terminal output with consistent styling. Used by dnstm, dnstc, and sshtun-user. Any component or logic shared across multiple projects is abstracted and centralized here.

#### [Net2Share Website (net2share.github.io)](https://github.com/net2share/net2share.github.io)

The official documentation website for Net2Share, hosted at [net2share.com](https://net2share.com). Built with mkdocs-material, it provides comprehensive guides for setting up and using DNS tunnel infrastructure.

- Step-by-step server setup guides for dnstm in English and Farsi
- Client setup guides for Windows, macOS, and Linux
- Troubleshooting and FAQ sections
- Project documentation and architecture overviews

### DNS Tunnel Implementations

#### [slipstream-rust-build](https://github.com/net2share/slipstream-rust-build)

An automated mirror of the upstream Slipstream Rust project that continuously syncs and builds fresh binaries via GitHub Actions. Whenever the upstream project updates, this repository automatically compiles and releases binaries for x86_64 and ARM64 Linux—so you always have access to the latest version without needing to build from source. dnstm fetches the latest binaries directly from this project's release artifacts.

#### [dnstt](https://github.com/net2share/dnstt)

A mirror of the original DNSTT project with pre-compiled binaries available in GitHub Releases. DNSTT is a userspace DNS tunnel that enables secure, covert communication through DNS queries using DoH (DNS over HTTPS), DoT (DNS over TLS), and standard UDP DNS. It uses Noise protocol encryption for end-to-end security, ensuring your traffic remains private even from intermediate DNS resolvers. dnstm fetches the latest binaries directly from this project's release artifacts.

#### [dnstt-revived](https://github.com/net2share/dnstt-revived)

A fork of DNSTT with improvements tailored for the current Iranian network conditions. This project is currently in the R&D phase with some promising results, aiming to extend the original implementation while preserving its strong cryptographic guarantees.

### Utilities

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

    subgraph dns_proxy_server["DNS Proxy (Port 53)"]
        P[Domain-Based Router]
    end

    subgraph transports["Transport Instances"]
        subgraph prod["Production Tunnels"]
            B1[Slipstream Instance 1<br/>domains: t1.example.com]
            B2[Slipstream Instance 2<br/>domains: t2.example.com]
            C1[DNSTT Instance<br/>domains: d.example.com]
        end
        subgraph health["Health Check Instances"]
            H1[Slipstream Health<br/>domains: hc-s.example.com]
            H2[DNSTT Health<br/>domains: hc-d.example.com]
        end
    end

    subgraph modes["Connection Modes"]
        D[Standalone Mode]
        E[Shadowsocks SIP003 Plugin]
    end

    subgraph shadowsocks["Shadowsocks Server"]
        F[shadowsocks-rust]
    end

    subgraph usermgmt["User Management"]
        M[sshtun-user]
        N[microsocks]
    end

    subgraph artifacts["Binary Sources"]
        J[slipstream-rust-build]
        K[dnstt releases]
        L[microsocks-build]
    end

    dnstm --> dns_proxy_server
    dns_proxy_server -->|route by domain| transports
    prod --> modes
    health -->|e2e validation endpoint| modes
    E --> shadowsocks
    dnstm --> usermgmt

    J -.->|fetches binaries| prod
    J -.->|fetches binaries| health
    K -.->|fetches binaries| C1
    K -.->|fetches binaries| H2
    L -.->|fetches binaries| N
```

### Client-Side Architecture

The client runs inside the restricted network and orchestrates the connection flow.

```mermaid
flowchart TB
    subgraph scanner["dnst-resolver-scanner"]
        direction TB
        A[ir-resolvers<br/>Raw IP List]
        S1[Step 1: Basic Scan<br/>Find working resolvers]
        S2[Step 2: E2E Validation<br/>Test with actual tunnels]
    end

    subgraph dns_proxy["Local DNS Proxy"]
        C[Load Balancer]
        D[Healthy Resolver Pool]
        MON[Health Monitor<br/>Continuous checking]
    end

    subgraph client["dnstc"]
        E[Orchestrator]
    end

    subgraph transport_proxy["Transport Load Balancer"]
        TLB[Load Balancer<br/>Higher aggregate bandwidth]
    end

    subgraph transports["Transport Instances"]
        T1[Slipstream Instance 1]
        T2[Slipstream Instance 2]
        T3[DNSTT Instance]
    end

    subgraph apps["Applications"]
        I[Browser / Apps]
    end

    subgraph remote["Server Health Check Instances"]
        R1[Slipstream Health Endpoint]
        R2[DNSTT Health Endpoint]
    end

    A -->|raw IPs| S1
    S1 -->|responding resolvers| S2
    S2 -->|tunnel-capable resolvers| D
    S2 -.->|e2e test via| R1
    S2 -.->|e2e test via| R2
    MON -->|update pool| D
    MON -.->|periodic check| R1
    MON -.->|periodic check| R2
    E --> scanner
    E --> dns_proxy
    E --> transport_proxy
    E --> transports
    I -->|traffic| TLB
    TLB --> transports
    transports -->|tunnel via| D
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
        B2[dnstc]
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
