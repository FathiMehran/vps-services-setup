# Xray Reality vs V2Ray Behind Cloudflare

This document compares two widely used proxy deployment approaches:
Xray with Reality and V2Ray behind Cloudflare using Nginx and WebSocket.

The goal is not to declare a universal winner, but to explain how each model
works, what problems it solves, and when it should be used.

---

## Conceptual Overview

### Xray with Reality

Xray Reality establishes a TLS-like connection without using real TLS certificates.
It mimics a legitimate TLS handshake and connects directly to the VPS.

There is no CDN, no reverse proxy, and no third-party TLS termination.
All cryptographic operations are handled by Xray itself.

---

### V2Ray Behind Cloudflare

In this model, V2Ray runs locally on the VPS and listens only on localhost.
Nginx terminates TLS and forwards WebSocket traffic to V2Ray.
Cloudflare acts as a CDN and hides the real VPS IP.

To the outside world, all traffic looks like standard HTTPS traffic handled by Cloudflare.

---

## High-Level Comparison

| Aspect | Xray + Reality | V2Ray + Cloudflare |
|------|---------------|-------------------|
| TLS handling | Built-in (Reality) | Cloudflare |
| Reverse proxy | Not required | Required (Nginx) |
| Domain required | Optional | Mandatory |
| VPS IP exposed | Yes | No |
| DPI resistance | Very high | Medium to high |
| Performance | High | Medium |
| Infrastructure complexity | Low | High |
| Dependency on third parties | None | Cloudflare |

---

## Xray + Reality

### Strengths

- Strong resistance against DPI and active probing
- No need for TLS certificates or CDN configuration
- Fewer components and simpler architecture
- Lower latency and better performance
- Easier troubleshooting due to minimal moving parts

### Limitations

- VPS IP is exposed to clients
- IP blocking directly affects availability
- Less suitable for hosting multiple services on the same domain
- Requires IP rotation if the server is blocked

### When Xray + Reality Makes Sense

Use this approach when:
- DPI evasion is the primary concern
- You prefer a minimal and direct setup
- Performance and low latency matter
- You are comfortable managing VPS IP changes if necessary

---

## V2Ray Behind Cloudflare (Nginx + WebSocket)

### Strengths

- VPS IP is hidden behind Cloudflare
- Domain-based access allows easier rotation and migration
- Can coexist with normal websites on the same domain
- Benefits from Cloudflare features such as WAF and rate limiting
- Traffic closely resembles normal HTTPS usage

### Limitations

- More components increase operational complexity
- Additional latency due to CDN and reverse prox

