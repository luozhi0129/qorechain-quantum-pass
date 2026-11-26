# QoreChain Quantum Pass — one hybrid-signed transaction, verified on chain

**Build Before Q-Day Developer Fest · Quantum Pass · solo · testnet only**

A minimal app that does exactly what the Quantum Pass level asks for: scaffold
with `create-qorechain-dapp`, connect a testnet wallet, deploy a Counter
contract, and send **one transaction signed twice** — once with a classical
secp256k1 key and once with a post-quantum ML-DSA-87 key — then read the
verification back from the public chain.

No mainnet, no real funds, no spending. Everything below runs on the
QoreChain testnet (`qorechain-diana`, EVM chain ID 9800).

## Live evidence (this run, 2026-11-26)

| Item | Value |
|---|---|
| Testnet wallet | `qor1ee0qtcm648zqxj56klpshpmhjkxquxjg757sh6` (`0xce5e05e37aa9c4034a9ab7c30b8777958c0e1a48`) |
| Hybrid-signed tx (window open) | `A5000B2F77086A5550FE546EA4651452F91B6EAA5F937CBE697E230C7F65EE74` |
| Window tx height / code / gas | 10,772,974 / 0 / 187,115 |
| `pqc_hybrid_verify` event | `address = qor1ee0q…`, `algorithm_id = dilithium5`, **`status = verified`** |
| ML-DSA-87 signature | **4,627 bytes** (extension `/qorechain.pqc.v1.PQCHybridSignature`, `algorithm_id: 1`) |
| secp256k1 signature | 64 bytes |
| Counter contract | `0x37C7Bdd018bDaf766f2772Fea425A49660a6d3aE` |
| Deploy tx | `0x2a4b10f4c9d42d791df177875898172a67e22ebe970dcddcccda73fa04340dd6` |
| Deploy receipt | `status: 0x1`, block 10,772,985, gas 138,061, runtime code 371 bytes |

Verify it yourself (no account needed):

```bash
# Hybrid transaction + verification event
curl -s "https://api-testnet.qore.host/cosmos/tx/v1beta1/txs/A5000B2F77086A5550FE546EA4651452F91B6EAA5F937CBE697E230C7F65EE74" \
  | python3 -c "import sys,json; d=json.load(sys.stdin); print(d['tx_response']['code'], d['tx_response']['height']); print([e for e in d['tx_response']['events'] if e['type']=='pqc_hybrid_verify'])"

# Deployment receipt (EVM)
curl -s -X POST https://evm-testnet.qore.host -H 'Content-Type: application/json' \
  -d '{"jsonrpc":"2.0","id":1,"method":"eth_getTransactionReceipt","params":["0x2a4b10f4c9d42d791df177875898172a67e22ebe970dcddcccda73fa04340dd6"]}' \
  | python3 -m json.tool
```

Or open the hashes on the explorer: https://explore.qore.network (switch to
Testnet). An earlier build of the same flow (window tx
`04AAC47C9C01DAD02B8D3C7E8F668C88AA21170EA857D585274BB6F5C6160983`, Counter
`0x5b5ed780c96E8cf715b55d649adB9C8928d618Ed`) is documented in
`quantum-pass-build-2026-11-26.md`.

## How to reproduce

Follows the official Workshop 1 guide:
https://docs.qorechain.io/build-before-qday/workshop-1
(scaffolder 0.14.0, SDK 0.14.0, checked 2026-11-26).

Prerequisites: Node.js ≥ 22, pnpm, git. The app lives in `qday-demo/`.

```bash
cd qday-demo

# 1. Install. pnpm 12 blocks build scripts by default, so approve esbuild
#    first — otherwise `pnpm install` fails with ERR_PNPM_IGNORED_BUILDS.
pnpm approve-builds esbuild
pnpm install

# 2. Local tests (Hardhat simulated chain, 2 passing)
pnpm test

# 3. Create your OWN throwaway testnet account and fund it from the faucet
#    (https://dashboard.qorechain.io/faucet, 100 QOR). Copy .env.example to
#    .env and fill in PRIVATE_KEY + QORE_PQC_RECOVERY_CODE (or QORE_MNEMONIC).
#    Never commit .env — it is git-ignored. Never use a wallet with real funds.
cp .env.example .env

# 4. Register the ML-DSA-87 key (no-op if already registered), then open the
#    authorisation window. EVM transactions cannot carry a post-quantum
#    signature, so the window is opened by a dual-signed message instead.
pnpm window register
pnpm window open

# 5. Deploy the Counter inside the window (600 blocks / 5 txs).
#    The window is short-lived: re-open it with `pnpm window open` before
#    any further EVM transaction, or the deploy fails with "no window".
pnpm deploy:testnet

# 6. Verify: read the window transaction back from the public REST endpoint
#    and check the pqc_hybrid_verify event (see the curl above).
```

## Project layout

```
qday-demo/                 Hardhat 3 project (scaffolded by create-qorechain-dapp)
  contracts/Counter.sol    The counter: stores one number, increments it
  ignition/modules/        Deployment module
  scripts/window.ts        Window helper (register / open / status)
  test/                    2 local tests
quantum-pass-build-2026-11-26.md   First-build record (earlier tx + contract)
demo-run.log               Verbatim log of the fresh demo run (window open → deploy → verify)
AI_USAGE.md                How AI was used in this project (Best AI-Native Build)
```

## Prize facts (official site, verified 2026-11-26)

Quantum Pass: the jury scores passing projects; the ten best win **$150**
each; the other passing projects enter a public drand draw for **20 × $50**.
Separate safety nets: Regional Award (Asia-Pacific) **$200** and Top-Quality
Awards **7 × $250** (jury ≥ 70/100, no other prize). Prizes are paid in
stablecoin 17–22 December 2026. Note: whether China/Singapore residents can
receive payouts is **not yet verified** — the official exclusion list is
published only before the lottery, and residence proof may be required.

## Licence

MIT — see `LICENSE`.
