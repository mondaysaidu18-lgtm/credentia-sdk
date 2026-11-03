# 🎓 Credentia SDK

> **A TypeScript library and CLI for Credentia. Hash a credential, check it against the Soroban registry, and build unsigned issue and revoke transactions, in a few lines. It never touches a secret key.**

![CI](https://github.com/mondaysaidu18-lgtm/credentia-sdk/actions/workflows/ci.yml/badge.svg)
![License: MIT](https://img.shields.io/badge/License-MIT-green.svg)
![PRs Welcome](https://img.shields.io/badge/PRs-welcome-blue.svg)
![Stellar](https://img.shields.io/badge/Built%20on-Stellar-0e75dd.svg)

| 🔗 Links | |
|---|---|
| 📜 [Registry Contract](https://github.com/mondaysaidu18-lgtm/credentia-registry) | 🌐 [Credentia Platform](https://github.com/mondaysaidu18-lgtm/credentia-platform) |
| 📚 [API reference](docs/api.md) | 🐛 [Report a Bug](https://github.com/mondaysaidu18-lgtm/credentia-sdk/issues) |

**Jump to:** [About](#-about) · [Features](#-features) · [Install](#-install) · [Usage](#-usage) · [Hashing](#-how-a-credential-is-hashed) · [Testing](#-testing) · [Security](#-security) · [Roadmap](#-roadmap)

---

## 📖 About

> Anyone who receives a credential, such as an employer, a school or another app, needs a simple way to check it.

Credentia SDK does three things:

| | What it does | How |
|:-:|---|---|
| **1️⃣** | **Fingerprints a credential** | the same way every time, using canonical JSON and SHA-256, so every tool gets the same hash. |
| **2️⃣** | **Verifies it** | by asking the [credentia-registry](https://github.com/mondaysaidu18-lgtm/credentia-registry) contract, with no wallet and no fee. |
| **3️⃣** | **Builds unsigned transactions** | for issuing and revoking. Your wallet or signer signs them, never this library. |

The SDK is used by [credentia-platform](https://github.com/mondaysaidu18-lgtm/credentia-platform) and can be used on its own in any Node.js or browser app.

---

## ✨ Features

| | Feature | Details |
|:-:|---|---|
| 🔢 | **Canonical hashing** | `canonicalize` sorts keys and normalises text, then `hashCredential` returns the SHA-256 hash |
| ✅ | **Read-only verify** | `verify` and `verifyCredential` return one of `Valid`, `Revoked`, `Expired`, `NotFound`, `HashMismatch` or `IssuerSuspended` |
| 🧾 | **Unsigned transactions** | `buildIssue`, `buildRevoke` and `buildRegisterIssuer` return XDR ready to sign |
| 🔍 | **Record reads** | `getCredential` and `getIssuer` |
| 💻 | **CLI** | `credentia verify credential.json` for scripts and quick checks |
| 🌐 | **Verify widget** | a small `<credentia-verify>` web component you can drop into any page |
| 🔒 | **Keyless** | the library never sees or signs with a secret key |

---

## 🚀 Install

```bash
npm install @credentia/sdk
```

Requires Node.js 20+.

---

## 💡 Usage

### 📦 Library

```ts
import { CredentiaClient, hashCredential } from "@credentia/sdk";

const client = new CredentiaClient({
  contractId: "<REGISTRY_CONTRACT_ID>",
  rpcUrl: "https://soroban-testnet.stellar.org",
  networkPassphrase: "Test SDF Network ; September 2015",
});

// Check a credential someone gave you
const result = await client.verifyCredential(credentialJson);
console.log(result.status); // "Valid" | "Revoked" | "Expired" | ...

// Build an unsigned issue transaction. Your wallet signs it.
const hash = hashCredential(credentialJson);
const { xdr } = await client.buildIssue({
  issuer: issuerPublicKey,
  id: credentialId,
  holder: holderPublicKey,
  hash,
  expiresAt: 0,
});
```

### 💻 CLI

```bash
npx @credentia/sdk verify credential.json --contract <REGISTRY_CONTRACT_ID> --network testnet
```

The command prints the result and exits with code 0 only when the credential is `Valid`, so it works in scripts and CI.

### 🌐 Verify widget

```html
<script type="module" src="https://unpkg.com/@credentia/sdk/dist/widget.js"></script>
<credentia-verify contract="<REGISTRY_CONTRACT_ID>" network="testnet"></credentia-verify>
```

The widget lets a visitor drop a credential file and see the result. The check runs in the browser.

---

## 🔄 How a credential is hashed

```
credential JSON -> remove the "proof" field -> sort keys, normalise Unicode text, no extra whitespace
-> UTF-8 bytes -> SHA-256 -> 32-byte hash stored in the registry
```

The same input always gives the same hash, on any machine and in any language that follows the rules in [docs/api.md](docs/api.md).

---

## 🧪 Testing

```bash
npm ci
npm run lint
npm run typecheck
npm test
npm run build
```

Tests cover canonicalisation (key order, whitespace, Unicode, nested values), hash stability against fixed test vectors, every verification result with a mocked RPC, transaction building, and the CLI's exit codes.

---

## 🔐 Security

- The SDK never signs and never handles secret keys.
- Verification is only as trustworthy as the issuer. Check the issuer's accreditation, not just the result.
- To report a vulnerability, see [SECURITY.md](SECURITY.md).

> ⚠️ **This is unaudited testnet software.**

---

## 🗺️ Roadmap

Not built yet:

- [ ] Python client
- [ ] Batch verification
- [ ] Selective-disclosure helpers
- [ ] React component wrappers

---

## 🤝 Contributing

See [CONTRIBUTING.md](CONTRIBUTING.md). Pull requests target `main` and CI must pass.

## 📜 License

[MIT](LICENSE) © Credentia
