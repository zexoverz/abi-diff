# abi-diff

Tell whether a new contract ABI breaks the callers of the old one.

Upgrading a proxy, redeploying a contract or bumping an SDK ships a new ABI. Frontends, indexers and
subgraphs break quietly when a function signature changes, an event loses an `indexed` flag (so its
topics move), or a new function lands on a selector someone else already uses. npm has storage-layout
diffs for Hardhat, but nothing that compares two ABIs and says what breaks.

## Planned API

```ts
import { diffAbi } from "abi-diff";

const report = diffAbi(oldAbi, newAbi);
// report.breaking: [{ code: "function-removed" | "function-signature-changed" | "event-topic-changed" | "error-removed" | "mutability-tightened" | "selector-clash", item, detail }]
// report.safe:     [{ code: "function-added" | "event-added" | "output-renamed", item }]
// report.level:    "major" | "minor" | "patch"
```

- Compares functions, events, errors and the constructor by selector and topic, not by name.
- Catches overload changes, `indexed` changes, `view` becoming `nonpayable`, removed custom errors.
- A `selector-clash` check across both ABIs and optional extra ABIs (for a diamond or a proxy admin).
- CLI: `npx abi-diff old.json new.json`, exit code 1 on a breaking change so CI can gate on it.

Built at ETHGlobal Tokyo 2026 as a working project of the End Credits demo: the library is written in
a Claude Code session, and End Credits pays the open-source packages that session used.

## License

MIT
