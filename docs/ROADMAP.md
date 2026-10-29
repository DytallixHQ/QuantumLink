# QuantumLink roadmap

QuantumLink is **in development**. This roadmap lists stages, not dates. An
item moves forward when its evidence exists, not when a calendar says so.

## Where things stand

| Area | Stage |
|------|-------|
| Shared protocol core (identity, sessions, frames, replay, records, discovery, relay) | Implemented, tested in development |
| macOS app and packet tunnel | Implemented baseline; needs signed, notarized builds validated on real hardware |
| Windows tunnel service and app | Alpha |
| Steam / SteamOS edition | Planning |
| QuantumLink Agent | Design scaffold |
| Public identity registry | Designed; waiting on a live network (see below) |

## Before a pilot

- A supported build with a recorded build identifier and cryptographic profile.
- Agreed scope: endpoints, operating systems, traffic in scope, routing, and
  failure behavior.
- Acceptance tests: connection, traffic-leak checks, install, removal, and
  rollback.

## Before general availability

- [ ] Signed and notarized macOS app and tunnel extension, validated on real
      hardware under live traffic.
- [ ] Windows beta on the signed installer.
- [ ] Hardening evidence for publicly deployed rendezvous and relay services.
- [ ] Standards-complete NAT traversal and relay behavior against public
      infrastructure.
- [ ] A post-quantum signature over update manifests and releases.
- [ ] Device-key rotation and revocation end to end.
- [ ] Independent security review.

## Public identity registry

QuantumLink's public-mesh design checks peer identity against a registry on
the Dytallix blockchain. The registry was modeled as a contract on the
Dytallix testnet. Two facts shape what happens next:

- The Dytallix public testnet was retired on 4 October 2026.
- Dytallix mainnet v1 has no contract runtime.

Until a registry is live on a public network, QuantumLink runs with private
and development mesh policies, which do not need it. How the registry lands
on mainnet is an open design question and will be documented here when it is
decided. Follow Dytallix mainnet progress at
[dytallix.com/developers/launch](https://dytallix.com/developers/launch).

## Later

- SteamOS edition: game-aware routing, Steam-safe bypass, streamer modes,
  controller-friendly interface.
- QuantumLink Agent: assisted diagnostics, route and relay recommendations,
  approval-gated fixes with audit records.
- Accountless paid access through signed entitlements.
- Managed-device attestation and single sign-on integration.

## Influence the roadmap

Open an issue with the use case, the platform, and what would need to be true
for you to rely on QuantumLink. See [CONTRIBUTING.md](../CONTRIBUTING.md).
