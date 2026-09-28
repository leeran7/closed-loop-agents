# Learnings Ledger

_Last curated: never._

Kernel quality gates (every repo): `skills/closed-loop/gates.md`.
This file is **this repo's** memory. Do not copy it into other products.

## Standing rules (always apply)

## By topic
### Testing
### Security
- Raw CDP WebSocket framing: set the RFC6455 mask bit AND send the 4-byte
  masking key (mask bit without key = silent hang); support 16-bit and
  64-bit extended payload lengths in both directions — screenshots and
  setDocumentContent payloads exceed 64KB. Flattened-mode `sessionId` goes
  top-level in the CDP message, not inside `params`.
- Chrome >= ~132 blocks top-level navigation to loopback http with
  `ERR_BLOCKED_BY_LOCAL_NETWORK_ACCESS_CHECKS`, even with
  `--proxy-server=direct://`. For a headless render harness, skip the server
  entirely: `Page.setDocumentContent` + three.js inlined as a hash-verified
  `data:` URL in the import map. No server, no navigation, no proxy.
- Never run untrusted model JavaScript on `file://` with
  `--allow-file-access-from-files` — page JS can then read arbitrary local
  files via fetch().
- Untrusted code in a harness page: base64-encode it and dynamic-import via
  a blob URL inside try/catch. This structurally eliminates `</script`
  breakouts (raw code never touches the HTML parser) and surfaces top-level
  throws with their message instead of a gate timeout. Add
  `connect-src 'none'` CSP so generated code cannot exfiltrate.
### Architecture & contracts
### Performance
### Spec quality
### Build / CI
### Orchestration

## Open questions (unresolved, need a decision)

## Recently applied (last 20)
