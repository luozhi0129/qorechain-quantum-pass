# AI Usage

This project was built with an AI agent, and this file documents the workflow,
what the AI generated, and where the human contributed — for the Best
AI-Native Build special award.

## Workflow

1. **Human decisions (Super Hakimi):** chose the Quantum Pass level, created
   the testnet wallet and claimed the 100 QOR faucet funds at registration,
   set the project direction (follow the official Workshop 1 guide, verify
   everything against the public chain), and reviews this package before
   submission.
2. **Agent execution (Muse, an AI coding agent):** scaffolded the app with
   the official `create-qorechain-dapp` (v0.14.0), ran the guide's commands
   (`pnpm test`, `pnpm window open`, `pnpm deploy:testnet`), and verified
   every on-chain result against the public REST/RPC endpoints — the
   `pqc_hybrid_verify` event, the 4,627-byte ML-DSA-87 signature, the deploy
   receipt, and the 371 bytes of runtime code. No result was taken on trust;
   each number in the README traces to a command output in `demo-run.log`.
3. **Agent-authored artifacts:** this README, the demo-run log, the demo
   video (terminal footage of a real run, narration synthesized with
   edge-tts), the cover image, and the AI-usage documentation itself.
4. **Human review:** the human operator reviews the full package — code,
   evidence, video, and this file — before the project is submitted.

## What the AI did NOT do

- No code was written from scratch by the AI: the contract, deployment
  module, and window helper all come from the official QoreChain scaffolder
  and SDK (`@qorechain/sdk` 0.14.0). The agent's job was to run the official
  flow, fix the one environment issue it hit (`pnpm approve-builds esbuild`
  on pnpm 12), and prove the result on chain.
- No transaction was ever signed by the AI on its own initiative: every
  on-chain action used the human-created testnet wallet, on the testnet only.
- No number in the README or video is invented: each one is quoted from a
  real command output, and the full log is kept in `demo-run.log`.

## Tools used

- QoreChain scaffolder + SDK 0.14.0, Hardhat 3, pnpm 12, Node 24
- edge-tts (`en-US-AriaNeural`) for the demo narration, ffmpeg for assembly
- Public QoreChain testnet REST (`api-testnet.qore.host`) and EVM RPC
  (`evm-testnet.qore.host`) for verification
