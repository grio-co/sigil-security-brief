# Sigil & sigilOS — Security Posture Brief

*A plain-language summary of how we approach security for the Sigil language and
sigilOS, and what our internal review found at a high level.*

**Published by GrioCo · 2026**

Last reviewed: 2026-09-26

Version: 0.1.0 <!-- PLACEHOLDER(greg): confirm version; no git tags exist, so 0.1.0 is assumed -->

---

## Why we're publishing this

Most companies talk about security only after something goes wrong. We're doing
the opposite: while Sigil and sigilOS are still in active pre-release
development, we commissioned an internal red-team / blue-team security review of
the entire stack and are sharing what that process looks like and what it told
us. We think how a team treats security *before* 1.0 says more than any
post-incident statement.

This is a high-level brief for a general audience. It deliberately contains **no
exploit details, code locations, or step-by-step weaknesses** — responsible
disclosure means the specifics stay internal until they're resolved.

---

## What Sigil is trying to do differently

Sigil is a programming language, and sigilOS is an operating system built in it,
designed around a simple but demanding idea: **security should be structural, not
optional.**

- **No ambient authority.** In most software, any line of code can quietly open a
  file, reach the network, or touch the hardware. In Sigil, code can only do what
  it was explicitly handed permission ("a capability") to do — and those
  permissions are visible right in the code. A library that was never given
  filesystem access simply *cannot* reach your files.
- **Permissions you can see.** Every function declares what it's allowed to do.
  If it isn't declared, it can't happen. That makes code far easier for a human
  to audit — which matters more than ever now that so much code is written by AI.
- **Provable correctness.** Where possible, Sigil mathematically proves that code
  meets its stated contract, rather than just testing it.

The goal is to make entire categories of vulnerability *impossible to express*,
instead of merely discouraged. It's an ambitious bet, and we hold ourselves to
it honestly.

---

## How we reviewed it

We ran a structured security assessment across the whole stack — the language and
compiler, the microkernel, the filesystem, device drivers, the graphics and video
subsystem, the userland applications and networking, the documentation tooling,
and our build-and-release pipeline.

Each area was examined from two sides:

- **Red team** — actively trying to break it: how could an attacker defeat a
  protection, corrupt memory, or gain access they shouldn't have?
- **Blue team** — assessing the defenses: where do the safeguards genuinely hold,
  and how would we make them stronger?

Every finding was written up with a technical explanation, a plain-language
summary, and tiered options for fixing it — so both engineers and decision-makers
can weigh the trade-offs.

---

## What we found — in honest terms

The most important takeaway is one we want to state plainly:

> **The design is strong. The implementation is early, and the security
> guarantees currently describe the architecture more fully than the running
> system does.**

In other words, the ideas hold up — and in the parts of the system that are fully
built, they work as intended. But Sigil and sigilOS are pre-1.0, and our review
confirmed that some of the security enforcement that the design promises is not
yet wired into every shipping component. That's normal for software at this
stage; the point of reviewing now is to close those gaps *before*, not after,
people depend on them.

At a high level, the areas we're hardening fall into a few categories:

- **Consistent input validation.** Wherever the system accepts data from an
  untrusted source — a file, a device, the network — we're standardizing the
  checks applied before that data is used. This is the single largest theme and
  the one we're prioritizing.
- **Carrying the security model into every layer.** Some guarantees are fully
  enforced in our reference implementations and need to be brought uniformly into
  the components that run in production.
- **Production-grade credential and identity handling.** Several
  authentication mechanisms in the current build are development placeholders; we
  are replacing them with real, industry-standard cryptography before any
  multi-user or networked use.
- **Conventional web and application hardening** for our documentation and
  tooling.
- **Supply-chain and development-process controls**, including how our build
  pipeline and automation are permissioned and reviewed.

### What's already working well

Our review also confirmed real strengths worth naming:

- The core capability architecture is sound where it's implemented — permissions
  only ever *narrow*, never silently widen.
- Our reference toolchain correctly enforces the model it describes.
- **No secrets, credentials, or keys are exposed** anywhere in our source
  repositories.
- Our continuous-integration pipeline includes strong protections against a
  well-known class of build-system attacks.
- Multiple parsers and security-sensitive paths are implemented correctly and
  serve as the internal standard we're bringing the rest of the code up to.

---

## What happens next

We've turned the review into a prioritized remediation plan, sequenced so that
the most impactful, systemic fixes land first — and structured so that broad
categories of issues close together rather than one at a time. We've also adopted
a standing engineering rule that no placeholder or development stub may ship
without its production implementation in place, enforced automatically in our
build system.

We will not describe Sigil or sigilOS to customers as "capability-secure,"
"fault-isolated," or "verified" in production terms until the running system
earns those words — not just the design. Holding that line is, we think, the
whole point.

---

*This brief intentionally omits technical specifics. Security researchers who wish
to report an issue responsibly can contact us through our security disclosure
channel.*
