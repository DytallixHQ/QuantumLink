# QuantumLink security model

This is a summary of what QuantumLink protects, against whom, and where its
protection ends. The technical paper has the full treatment:
[quantumlink-technical-whitepaper.pdf](https://dytallix.com/whitepapers/quantumlink-technical-whitepaper.pdf).

## Cryptographic profile

| Purpose | Algorithm | Standard |
|---------|-----------|----------|
| Session key establishment | ML-KEM-768 | NIST FIPS 203 |
| Device credentials and signed peer records (default) | ML-DSA-65 | NIST FIPS 204 |
| Device credentials (hash-based option) | SLH-DSA, small-signature parameter set | NIST FIPS 205 |
| Transcript binding and key derivation | SHAKE256 with role-separated labels | NIST FIPS 202 |
| Packet confidentiality and integrity (release profile) | ChaCha20-Poly1305 | RFC 8439 |
| Local peer cache at rest (optional) | ChaCha20-Poly1305 | RFC 8439 |

The development build also contains an experimental SHAKE256-based frame
protector. It uses its own suite identifier, is disabled in release builds,
and will not be offered for production use without a published proof and
independent cryptographic review.

Rules the protocol enforces:

- **No classical key-exchange fallback.** If post-quantum establishment fails,
  there is no session.
- **Suite binding.** The negotiated suite is bound into the transcript, so a
  downgrade is detected and rejected.
- **Fresh, directional keys.** Each session gets new keys, with a separate key
  for each direction.
- **Replay rejection.** Each direction keeps a monotonic packet-number window.

## What QuantumLink protects

| Goal | How |
|------|-----|
| Confidentiality against harvest-now, decrypt-later | Post-quantum session establishment; recorded traffic cannot be decrypted later by breaking a classical key exchange. |
| Peer authenticity | Post-quantum device signatures on records and handshakes; registry status checks for public meshes. |
| Integrity and freshness | Authenticated frames, transcript binding, replay windows. |
| No silent leaks | Kill switch, protected routes, DNS policy. Failures drop traffic. |
| Metadata minimization | Traffic, DNS, routes, endpoints, and session keys are never placed in the registry. Diagnostics are redacted by default. |
| Small trust footprint | Rendezvous and relays are untrusted: they cannot forge records or read frames. |

## Trust boundaries

1. **Device ↔ OS secret store.** Private keys stay in Keychain or DPAPI.
2. **App ↔ privileged tunnel.** The tunnel runs with elevated rights; the UI
   does not. They talk over a narrow, typed interface.
3. **Device ↔ rendezvous.** Records are signed and expire; rendezvous is not
   trusted.
4. **Device ↔ relay.** Relays see encrypted frames and timing, not content.
5. **Device ↔ registry.** The registry is trusted only for identity status
   and key bindings, and is checked before public-mesh admission.
6. **Device ↔ peer.** Authenticated by the handshake and the mesh's trust
   policy.

## Adversaries considered

- A network observer recording traffic now to decrypt later.
- An active network attacker trying to impersonate, downgrade, or replay.
- A malicious or compromised rendezvous or relay operator.
- A revoked, suspended, or impersonating peer.
- A local unprivileged process trying to reach the tunnel's privileged
  interface.
- Someone reading support bundles or logs.

## Out of scope and limits

- **Endpoint compromise.** Malware or a compromised OS on either peer can read
  data before encryption or after decryption.
- **Anonymity.** QuantumLink minimizes metadata but is not an anonymity
  network. Relays and network observers can see that two parties communicate
  and when.
- **Application flaws and access-control mistakes** inside the services you
  reach over the mesh.
- **Identity mistakes.** Enrolling the wrong device or trusting the wrong peer.
- **Registry availability.** Public meshes reject peers whose identity state
  is unavailable; this is deliberate fail-closed behavior.

## Still to be proven before release

These are tracked openly in the [roadmap](ROADMAP.md):

- Hardening evidence for publicly deployed rendezvous and relay services.
- Signed and notarized builds validated on real hardware under live traffic.
- A post-quantum signature over update manifests and releases.
- A live public identity registry and its lifecycle (registration, rotation,
  revocation).
- An independent security review.

## Reporting a vulnerability

See [SECURITY.md](../SECURITY.md).
