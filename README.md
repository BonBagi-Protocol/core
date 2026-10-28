# @bonbagi/core

[![CI](https://github.com/BonBagi-Protocol/core/actions/workflows/ci.yml/badge.svg)](https://github.com/BonBagi-Protocol/core/actions/workflows/ci.yml) ![tests](https://img.shields.io/badge/tests-39%20passing-2ea44f) ![license](https://img.shields.io/badge/license-MIT-blue)

**The rules every BonBagi page, wallet and verifier agrees on.** One Zcash shielded payment, split to every member of a collective in a single transaction: this package holds the signed split charter, the deterministic zatoshi split, the ZIP-321 multi-recipient request, the per-member memo and the pay link. Pure TypeScript, no network, no storage. Built for ZECATHON 2026 (Shielded Payments).

Used by **[BonBagi-Protocol/frontend](https://github.com/BonBagi-Protocol/frontend)** (live: https://bonbagi.vercel.app).

## What it guarantees

| Module | Guarantee | Tested by |
|---|---|---|
| `charter.ts` | Every member signs the same canonical bytes (sorted keys, NFC strings, domain-separated) with Ed25519. Changing any address, share, title or minimum breaks every signature. | `test/core.test.ts` |
| `request.ts` | Refuses to build a payment unless every member's signature verifies. The receipt and the request come from the same array. | `test/core.test.ts`, `test/paylink.test.ts` |
| `split.ts` | Integer zatoshi only; largest-remainder rounding, ties to the first member; shares always sum to the price; a minimum per member avoids dust. | 50 members, 3-way ties, supply cap |
| `zip321.ts` | Builds and strictly parses ZIP-321 multi-recipient URIs (`address.1`, `amount.1`, base64url memos). Round-trips. | `test/core.test.ts` |
| `memo.ts` | `bonbagi:v1 o=<order> c=<fingerprint> m=<i>/<n>`; never above 512 bytes; no personal data. | longest memo test |
| `address.ts` | Shielded only: transparent addresses are rejected. | `test/core.test.ts` |
| `paylink.ts` | The signed charter travels in the URL fragment (`#c=`), which browsers never send to a server; ZIP-317 fee estimate. | `test/paylink.test.ts` |

There is no smart contract: Zcash has none. The "contract" is the signed charter plus one ZIP-321 request that the buyer's own wallet pays.

## Use

```bash
npm install github:BonBagi-Protocol/core#v0.1.0
```

```ts
import { buildPaymentRequest, verifyCharter, parseZec } from "@bonbagi/core";

const { unsigned } = await verifyCharter(signedCharter);   // [] = every member signed
const req = await buildPaymentRequest(signedCharter, parseZec("0.003"), "ord-k7m2qx9p");
req.uri;    // zcash:?address=…&amount=0.0018&memo=…&address.1=…&amount.1=0.0012&memo.1=…
req.lines;  // the receipt: label, address, share, amount, memo per member
```

## Develop

```bash
npm ci
npm test          # 39 unit tests (vitest)
npm run typecheck
npm run build     # ESM + .d.ts in dist/
```

## Status and limits

- v0.1.0, used in production by the BonBagi pay and charter pages.
- What it does not hide is listed in the frontend's [threat model](https://github.com/BonBagi-Protocol/frontend/blob/main/docs/THREAT-MODEL.md) (for example, the number of shielded actions is public).
- Backlog IDs `BB-xx` are shared across BonBagi repositories ([backlog](https://github.com/BonBagi-Protocol/frontend/blob/main/docs/BACKLOG.md)).

## Team

Dex Bennett ([@Stylenecy](https://github.com/Stylenecy)) · Hans ([@Shenhan01-sys](https://github.com/Shenhan01-sys)) · Dave ([@agadape](https://github.com/agadape)).

## License

MIT
