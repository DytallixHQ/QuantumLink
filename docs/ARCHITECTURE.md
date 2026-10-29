# QuantumLink architecture

QuantumLink separates four jobs and gives each one only the data it needs:

| Question | Answered by | Never sees |
|----------|-------------|------------|
| Is this peer who it claims, and is it allowed? | Identity registry and trust policy | Traffic, routes, DNS, endpoints |
| How do I reach it right now? | Signed peer records, rendezvous, relay | Plaintext, session keys |
| How is the traffic protected? | The session between the two peers | Nothing outside the two peers |
| What leaves this device, and when must it stop? | The native tunnel on the device | Nothing off the device |

Keeping these apart is what stops blockchain identity from becoming
blockchain surveillance.

## Components

```
            ┌───────────────────────── Device ─────────────────────────┐
            │                                                           │
            │  Native app (UI, settings, diagnostics)                   │
            │        │                                                  │
            │  Native tunnel (routes, DNS, kill switch, privileges)     │
            │        │                                                  │
            │  Shared protocol core                                     │
            │   identity · sessions · frames · replay · trust policy    │
            │   records · discovery · relay client · metrics            │
            │        │                         │                        │
            │  Secret store (Keychain / DPAPI) │                        │
            └────────┼─────────────────────────┼────────────────────────┘
                     │                         │
         signed peer records            encrypted frames
                     │                         │
              ┌──────▼──────┐          ┌───────▼────────┐        ┌──────────┐
              │ Rendezvous  │          │ Direct path or │◄──────►│ Peer     │
              │ (untrusted) │          │ relay (blind)  │        │ device   │
              └─────────────┘          └────────────────┘        └──────────┘
                     │
        identity status lookup (public meshes)
                     │
              ┌──────▼──────────────┐
              │ Identity registry   │
              │ (status, key hashes)│
              └─────────────────────┘
```

### Shared protocol core

One core implements identity, session cryptography, packet framing, replay
protection, trust decisions, peer records, discovery, relay coordination,
metrics, and tracing. Every platform uses the same core, so cryptographic and
identity behavior does not drift between operating systems.

### Native platform layers

Each platform wraps the core with what only the operating system can provide:

- **macOS**: SwiftUI app, Network Extension packet tunnel, Keychain, MDM and
  per-app VPN, signed and notarized packaging.
- **Windows**: a privileged tunnel service separated from the UI, a Wintun
  adapter, a Windows Filtering Platform kill switch, DPAPI key storage,
  named-pipe IPC, a WinUI 3 dashboard, MSI packaging.
- **SteamOS** (planned): a Linux tunnel service with game-aware routing and
  Steam-safe bypass rules.

### Rendezvous

A small service that stores and returns signed peer records. It helps peers
find each other but is not trusted: it cannot create or alter a record without
the peer's key, and records expire on their own.

### Relay

Used only when two peers cannot reach each other directly. A relay forwards
encrypted frames and cannot read them. Relays can require admission tokens
and rate-limit clients.

### Identity registry

For public meshes, a peer's identity is checked against a tamper-evident
registry before it is dialed or accepted. A registry record holds the peer ID,
the owner's authorization, hashes of the device and transport keys, and a
status (active, suspended, revoked). It holds no traffic, routes, DNS,
endpoints, or session keys.

The registry is designed to live on [Dytallix](https://github.com/DytallixHQ/dytallix).
See the [roadmap](ROADMAP.md) for its current state: the Dytallix testnet
was retired in October 2026, and Dytallix mainnet v1 has no contract
runtime. Private and development meshes do not depend on the registry.

## Connection flow

1. **Enroll.** The device creates its identity key. For a public mesh, the
   owner registers the device's key hashes in the registry.
2. **Choose trust.** The user joins a public, private, or development mesh,
   each with its own policy.
3. **Discover.** The device publishes a signed, expiring record and looks up
   the peer's record through rendezvous, local discovery, or an invitation.
4. **Check.** The device verifies the record's signature and expiry, applies
   the mesh's trust policy, and for public meshes confirms an active registry
   record that matches the keys.
5. **Establish.** The peers run a post-quantum handshake, bind the keys to the
   transcript, and derive a key for each direction.
6. **Select a path.** Direct candidates are probed first. A relay is used only
   if none succeeds. The last good path is remembered.
7. **Carry traffic.** The native tunnel applies route, DNS, and kill-switch
   policy. Frames are authenticated, encrypted, and checked against the replay
   window.
8. **Fail closed.** If identity, session, route, or tunnel checks fail,
   protected traffic is dropped and the reason is recorded in redacted
   diagnostics.

## Where data lives

| On the device | In the control plane | In the registry | Never placed in the registry |
|---------------|----------------------|-----------------|------------------------------|
| Private keys, wallet secrets, route and DNS policy, packet plaintext | Short-lived signed peer records, reachability candidates, relay coordination | Peer identity binding, status, key hashes, optional reputation | Packet contents, DNS activity, raw endpoints, routes and hostnames, session keys, traffic timing, relay path history, private keys |
