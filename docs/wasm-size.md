# Wasm Size Budget

The deployed contracts are optimized WebAssembly artifacts under `target/optimized/`. `scripts/check_wasm_size.sh` applies these limits:

| Limit | Bytes | Meaning |
|---|---:|---|
| Warning | `65536` | The artifact is above the preferred 64 KiB size and should be reviewed. The check still passes with a warning. |
| Error | `131072` | Hard 128 KiB boundary. The size check fails above this limit. |

The current optimized baseline should be recorded here after running `make wasm_size` in the pinned Soroban CLI environment:

| Artifact | Current optimized size |
|---|---:|
| `target/optimized/settlement_contract_opt.wasm` | Not measured in this checkout |
| `target/optimized/governance_contract_opt.wasm` | Not measured in this checkout |

Run the check with:

```bash
make wasm_size
# equivalent script after make optimize:
bash scripts/check_wasm_size.sh
```

Keeping Wasm small reduces deployment cost, makes upgrades easier to inspect and transport, and limits the persistent rent and execution footprint associated with contract code. A warning is a review signal, not an automatic rejection; exceeding the error boundary is a deployment-blocking size regression.

When trimming an artifact, preserve the release profile's `opt-level = "z"`, link-time optimization (`lto = true`), one codegen unit, and symbol stripping (`strip = "symbols"`) in the workspace `Cargo.toml`. Also remove unused exports and dependencies, avoid retaining debug information, and confirm that the optimized artifact still passes tests and the size check.
