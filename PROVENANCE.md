# Provenance

What every result here traces to, and every correction made to a result after it was published.
Measurements are never edited: a correction only ever adds what a run failed to record, from
evidence anyone can check, and is listed below with that evidence.

## What a result records

| What | Where in the result |
|---|---|
| The hidane measured | `targets.hidane.version`, `targets.hidane.git_commit`, `targets.hidane.binary_sha256` |
| The engine that measured (`hidane benchmark`) | `benchmark.engine.version`, `benchmark.engine.git_commit`, `benchmark.engine.binary_sha256` |
| The benchmark definitions and parameters | `benchmark.version`, `benchmark.config_sha256`, `benchmark.canonical` |
| The machine and its software | `environment_revision`, the definition in `environments/<name>-r<revision>.json` |
| The official emulator | `targets.official.version`, `targets.official.jar_sha256`, `targets.official.jar_verified` |
| When | `timestamp` (UTC, when the run started) |

The hidane commit of a **nightly** result is the commit of hidane's `main` that was built and
measured. A **release** result measures the release's published binary; its commit is the source
commit named by that binary's build provenance attestation, which hidane's release workflow signs
at the release tag. `index.json` repeats the hidane version and commit, the environment revision,
the official emulator's version and the timestamp of every result.

## Corrections

### 2026-10-13 release result (v0.4.0): hidane commit recorded

Files: `results/release/2026/10/2026-10-13-release.json`, the `.md` rendered from it, and
`index.json`.

**What changed.** `targets.hidane.git_commit` went from `null` to
`8a8106248ecb80b27936f07c49db674ae10cc6bd`. Nothing else in the result changed: no measurement,
statistic, sample, stability check or other field. The Markdown report was rendered again from the
corrected JSON (`hidane benchmark render`), by the renderer of the run's own engine (commit
`468f1950042b`), which renders the original JSON into the original report byte for byte; its one
changed line is the hidane line, which now names the commit. `index.json` was regenerated from the
results the way every publication regenerates it; its one changed value is this result's
`hidane_commit`.

**Why it was missing.** The run measured the published v0.4.0 binary rather than a build of its
own, and at the time the harness did not record the commit a published binary was built from.
It records it now, from the same attestation.

**Evidence.**

1. The result's `targets.hidane.binary_sha256` is
   `c634adbbe93126b48ba702644af4f6742af611e040ed8285fcb592ecfbbc77af`.
2. The v0.4.0 release asset `hidane-0.4.0-x86_64-unknown-linux-musl.tar.gz` (SHA-256
   `d2ac0c272e9d3da72d80575f19c4428c97732a31de6bbdfac2a5c200bcae2b5c`, as the release's
   `sha256sums.txt` lists it) contains the binary `hidane` with that SHA-256: it is the binary the
   run measured.
3. That asset's build provenance attestation verifies against hidane's release workflow at the
   tag (`gh attestation verify` with `--signer-workflow
   hidane-dev/hidane/.github/workflows/release.yml --source-ref refs/tags/v0.4.0`). Its signing
   certificate names the source commit `8a8106248ecb80b27936f07c49db674ae10cc6bd` and the ref
   `refs/tags/v0.4.0`, built on a GitHub-hosted runner by
   [workflow run 38326096922](https://github.com/hidane-dev/hidane/actions/runs/38326096922). Its
   transparency log entry (Rekor) dates from 2026-10-13T00:18:17Z, before the run started
   (01:22:47Z).
4. The tag `v0.4.0` (annotated) points to the same commit, `8a8106248ecb`.

`targets.hidane.git_dirty` and `targets.hidane.build_profile` stay `null`: the run did not record
them, and the attestation does not state them.

**Checking it yourself** (needs `gh`, signed in, and `sha256sum` or `shasum -a 256`):

```sh
gh release download v0.4.0 --repo hidane-dev/hidane \
  --pattern 'hidane-0.4.0-x86_64-unknown-linux-musl.tar.gz' --pattern sha256sums.txt
sha256sum --check --ignore-missing sha256sums.txt
gh attestation verify hidane-0.4.0-x86_64-unknown-linux-musl.tar.gz --repo hidane-dev/hidane \
  --signer-workflow hidane-dev/hidane/.github/workflows/release.yml \
  --source-ref refs/tags/v0.4.0 \
  --format json --jq '.[0].verificationResult.signature.certificate.sourceRepositoryDigest'
tar -xzf hidane-0.4.0-x86_64-unknown-linux-musl.tar.gz
sha256sum hidane-0.4.0-x86_64-unknown-linux-musl/hidane
git ls-remote https://github.com/hidane-dev/hidane 'refs/tags/v0.4.0^{}'
```

The commit that made this correction changes three lines and nothing else: one in the result,
one in its report, one in the index.
