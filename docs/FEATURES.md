# QuantumLink features and functions

This page lists what QuantumLink does from a user's and an operator's point of
view. Each item is marked with its stage:

- **Baseline**: implemented in the development build and covered by tests.
- **Alpha**: implemented on some platforms, still being validated.
- **Planned**: designed, not yet implemented or not yet proven.

QuantumLink is in development. Nothing here is a production guarantee until
the item is listed as released in the [roadmap](ROADMAP.md).

## Identity

| Function | What it does | Stage |
|----------|--------------|-------|
| Device identity | Generates a persistent post-quantum device key (ML-DSA-65 by default). The peer ID is derived from the public key. | Baseline |
| Protected key storage | Keeps private key material in the platform's secret store (Keychain on macOS, DPAPI on Windows). | Baseline |
| Alternative signature suite | SLH-DSA (FIPS 205) device credentials for deployments that prefer hash-based signatures. | Baseline |
| Identity modes | **Verified**: check registry status without publishing a wallet address. **Public wallet**: deliberately show an operator identity. **Off**: no registry identity, for private or development meshes only. | Planned |
| Registry admission for public meshes | A public mesh admits a peer only when a matching, active identity record exists. Missing, revoked, suspended, or mismatched records are rejected. | Planned (see roadmap) |
| Rotation and revocation | Replace or withdraw a device key and stop peers from accepting the old one. | Planned |

## Trust and access policy

| Function | What it does | Stage |
|----------|--------------|-------|
| Mesh trust policy | Each mesh chooses its trust rules: public, private, or development. | Baseline |
| Peer access lists | Allow or deny specific peers. Keep a history of blocked peers. | Baseline |
| Inbound identity assertions | A connecting peer proves its identity before the session is accepted. | Baseline |
| Invitations | Exchange a trusted invitation to join a private mesh without a public lookup. | Baseline |
| Accountless entitlements | Paid access represented by a signed entitlement bound to an opaque subject. Billing stays outside the VPN path and never sees peer IDs, routes, DNS, or traffic. | Planned |

## Discovery and paths

| Function | What it does | Stage |
|----------|--------------|-------|
| Signed peer records | Peers publish short-lived, signed records of where they can be reached. Records expire and are re-published. | Baseline |
| Rendezvous lookup | Find a peer's current record. Rendezvous carries records but cannot forge them. | Baseline |
| Local discovery | Find peers on the same network without a server. | Baseline |
| Direct paths first | Probe direct candidates (including NAT traversal helpers) before anything else. | Baseline |
| Relay fallback | Use an authenticated relay only when no direct path works. The relay forwards encrypted frames it cannot read. | Baseline |
| Path memory | Cache the last good path and reconnect quickly after network changes. | Baseline |
| Encrypted peer cache | Store known peers locally, optionally encrypted at rest. | Baseline |

## Session protection

| Function | What it does | Stage |
|----------|--------------|-------|
| Post-quantum key establishment | ML-KEM-768 (FIPS 203) for every session. No classical key-exchange fallback. | Baseline |
| Transcript binding | Session keys are bound to the full handshake, the suite, and each side's role. | Baseline |
| Downgrade rejection | Peers refuse a weaker suite than the one both support. | Baseline |
| Replay protection | Each direction keeps a packet-number window and drops replays. | Baseline |
| Fresh keys per session | New session keys for every session; directional keys for each side. | Baseline |

## Tunnel, routing, and leak control

| Function | What it does | Stage |
|----------|--------------|-------|
| Native tunnel | Uses the operating system's own tunnel interface on each platform. | Baseline (macOS), Alpha (Windows) |
| Kill switch | Stops protected traffic when the tunnel or session fails instead of sending it unprotected. | Baseline (macOS), Alpha (Windows) |
| Route and DNS policy | Choose which routes and DNS queries go through the mesh. | Baseline |
| Per-app VPN | Send only selected applications through the mesh (managed macOS). | Baseline |
| Drop accounting | Count packets dropped by policy so failures are visible. | Baseline |
| Game-aware routing | Leave Steam store, wallet, checkout, and account-security traffic on the normal network; low-latency mode for game traffic. | Planned |

## Operations and diagnostics

| Function | What it does | Stage |
|----------|--------------|-------|
| Status views | Mesh, peers, routes, security, and activity at a glance. | Baseline |
| Redacted support bundles | Export diagnostics that explain a failure without exposing raw peer identifiers by default. | Baseline |
| Metrics | Optional local metrics endpoint for operators. Off unless configured. | Baseline |
| Managed deployment | MDM profiles for defaults, extension pre-approval, strict kill switch, and on-demand rules. | Baseline (macOS) |
| Native packaging | Signed and notarized macOS builds; MSI installer on Windows. | Planned for release |
| QuantumLink Agent | Explains trust and path failures, recommends routes and relays, and proposes fixes that a person must approve. Every action is logged. | Planned |

## What QuantumLink does not do

- It does not provide anonymity beyond minimizing the metadata it stores.
- It does not put traffic, DNS activity, routes, endpoints, or session keys on
  any blockchain.
- It does not secure the endpoint itself. See the
  [security model](SECURITY-MODEL.md).
