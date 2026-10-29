# QuantumLink

**Post-quantum mesh connectivity with verifiable peer identity.**

QuantumLink connects devices through an encrypted mesh. Devices talk to each
other directly where they can and use a relay when they must. Sessions are
established with NIST post-quantum cryptography, and peers in a public mesh
prove who they are before any traffic flows.

> **Status: in development.** QuantumLink is not generally available. Pilots run
> on an agreed build with a limited scope. See [Roadmap](docs/ROADMAP.md).

This repository is the public home for QuantumLink's product description,
features, architecture, and security model. It is written in the open so that
users, researchers, and auditors can examine the design before relying on it.
It does not contain product source code.

## The idea in one line

**Identity on-chain. Traffic off-chain. Access accountless. Transport
server-minimized.**

- **Identity** is a key held on the device, not an account with a provider.
- **Trust** is checked before a peer is admitted to a public mesh.
- **Traffic** never touches a blockchain. Packet data, routes, DNS, endpoints,
  and session keys stay on encrypted direct or relay paths.
- **Infrastructure** is kept small. There is no mandatory gateway in the middle
  of every session.

## Who it is for

| Audience | What QuantumLink gives them |
|----------|-----------------------------|
| Technical individuals | Reach laptops, home labs, storage, and private services without running a gateway VPN. |
| Remote teams | Connect people and devices with identity-bound policy instead of broad shared credentials. |
| Developers | Reach development machines and test environments without exposing them to the public internet. |
| Managed environments | Per-app VPN, MDM defaults, device policy, and native OS controls. |
| Journalists and sensitive work | Reduce long-term exposure of communications with known devices. |
| Gamers and streamers | Trusted peer paths that leave store, checkout, and account-security traffic alone. |
| Researchers and auditors | An inspectable protocol and trust model, documented here and in the papers. |

## What it does

| Capability | Summary |
|------------|---------|
| Verified peers | Check device identity before a connection. Decide which peers a mesh trusts. |
| Protected sessions | Post-quantum session establishment (FIPS 203) against harvest-now, decrypt-later collection. |
| Direct paths | Connect peers directly when reachable. Fall back to an authenticated relay when not. |
| Traffic policy | Choose the routes and applications in scope, and when traffic must stop if protection fails. |
| Fail-closed tunnel | Kill switch, protected routes, and DNS policy. A failure drops traffic instead of leaking it. |
| Redacted diagnostics | Explain trust and path failures without exposing raw peer identifiers by default. |

Full list: [docs/FEATURES.md](docs/FEATURES.md).

## How a connection works

1. **Create.** The app generates a persistent device identity (FIPS 204) and
   keeps the private key in the platform's protected secret store.
2. **Verify.** The peer is checked against the mesh's trust policy. Public
   meshes require a matching identity record; private meshes can use a lighter
   policy.
3. **Discover.** A signed peer record that expires advertises fresh routes and
   reachable addresses. The rendezvous service that carries it is not a trust
   anchor.
4. **Connect.** Peers authenticate, establish fresh FIPS 203 session keys, try
   direct paths, and select a relay only when needed.
5. **Protect.** The native tunnel applies routing, DNS, and kill-switch policy.
   Authenticated, encrypted frames carry the traffic.

More detail: [docs/ARCHITECTURE.md](docs/ARCHITECTURE.md).

## What it is not

QuantumLink is not an anonymity network, a geo-unblocking service, or a full
enterprise ZTNA or SD-WAN replacement. Its job is authenticated private
reachability.

Connection protection does not secure the whole endpoint. Malware,
application defects, identity mistakes, and access-control errors need
separate treatment, and data can be exposed after an endpoint decrypts it.
See [docs/SECURITY-MODEL.md](docs/SECURITY-MODEL.md).

## Platforms

One shared protocol core, wrapped natively on each platform.

| Platform | Native integration | Stage |
|----------|--------------------|-------|
| macOS | SwiftUI, Network Extension, Keychain, MDM and per-app VPN | Implemented baseline |
| Windows | Privileged tunnel service, Wintun, WFP kill switch, DPAPI, WinUI 3 | Alpha |
| Steam / SteamOS | Game-aware routing, Steam-safe bypass, streamer modes | Planning |
| QuantumLink Agent | Assisted diagnostics and approval-gated remediation | Design scaffold |

## Documentation

- [Features and functions](docs/FEATURES.md)
- [Architecture](docs/ARCHITECTURE.md)
- [Security model and cryptographic profile](docs/SECURITY-MODEL.md)
- [Roadmap](docs/ROADMAP.md)
- Product paper: [dytallix.com/whitepapers/quantumlink-product-whitepaper.pdf](https://dytallix.com/whitepapers/quantumlink-product-whitepaper.pdf)
- Technical paper: [dytallix.com/whitepapers/quantumlink-technical-whitepaper.pdf](https://dytallix.com/whitepapers/quantumlink-technical-whitepaper.pdf)
- Product page: [dytallix.com/ecosystem/quantumlink](https://dytallix.com/ecosystem/quantumlink)

## Taking part

Questions, design critique, threat-model challenges, and use-case requests are
welcome in Issues and Discussions. See [CONTRIBUTING.md](CONTRIBUTING.md).
Report vulnerabilities privately: [SECURITY.md](SECURITY.md).

To discuss a pilot, contact Dytallix at
[dytallix.com/contact](https://dytallix.com/contact?topic=QuantumLink).

## License

The documentation in this repository is licensed under
[Creative Commons Attribution 4.0](LICENSE). The QuantumLink and Dytallix
names and logos are trademarks of Dytallix and are not covered by that
license.
