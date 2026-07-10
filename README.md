# 🛡️ Rust Audit Check Action

Run [`cargo audit`](https://github.com/RustSec/rustsec/tree/main/cargo-audit) in GitHub Actions and fail the job when RustSec advisories are found.

This action is intentionally small. It does not use Node.js, Docker, the Checks API, or provide GitHub issue creation. It installs `cargo-audit`, runs it, and lets `cargo audit`'s output and exit status drive the workflow result.

The goal is to provide a much more maintainable alternative to [`rustsec/audit-check`](https://github.com/rustsec/audit-check) and [`actions-rust-lang/audit`](https://github.com/actions-rust-lang/audit) by keeping the action focused on the core audit workflow and avoiding being exposed to GitHub deprecation warnings.

## Usage

```yaml
name: Security audit

on:
  push:
    paths:
      - "**/Cargo.toml"
      - "**/Cargo.lock"
      - "**/audit.toml"
  pull_request:
    paths:
      - "**/Cargo.toml"
      - "**/Cargo.lock"
      - "**/audit.toml"
  schedule:
    - cron: "0 0 * * *"
  workflow_dispatch:

jobs:
  audit:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v7
      - uses: actions-rust-lang/setup-rust-toolchain@v1
        with:
          toolchain: stable
          rustflags: ""
      - uses: pirafrank/audit-check-action@v1
```

## Inputs

| Name | Required | Default | Description |
| --- | --- | --- | --- |
| `ignore` | No | `""` | Comma-separated list of advisory IDs to ignore. Use full IDs such as `RUSTSEC-2020-0036`. |
| `working-directory` | No | `.` | Directory containing the `Cargo.lock` file to audit. |

## Ignoring Advisories

Pass the full RustSec advisory ID to `ignore`:

```yaml
- uses: pirafrank/audit-check-action@v1
  with:
    ignore: RUSTSEC-2020-0036,RUSTSEC-2021-0145
```

For more detailed configuration, prefer a checked-in `audit.toml` file supported by `cargo audit`.

## Requirements

The runner must have Rust and Cargo available. The usage example installs the stable Rust toolchain with [`actions-rust-lang/setup-rust-toolchain@v1`](https://github.com/actions-rust-lang/setup-rust-toolchain), which uses rustup's minimal profile for explicit toolchain installs.

> [!IMPORTANT]
> The required Rust version depends only on [`cargo-audit`](https://github.com/RustSec/rustsec/tree/main/cargo-audit). For example, the Rust version pinned in your `rust-toolchain.toml` may be too old for the current `cargo-audit` release.
>
> This action does not install Rust or change the version already present in the environment. Install Rust before running this action if your runner does not already provide a suitable toolchain.

## License

MIT
