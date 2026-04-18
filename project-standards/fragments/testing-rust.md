<!-- tier: project -->
<!-- requires: testing-universal -->

## Rust Testing — Stack-Specific Commands

### Running Tests

```bash
# Run all tests
cargo test

# Run tests with output shown (including passing tests)
cargo test -- --show-output

# Run a specific test by name
cargo test test_name

# Run only integration tests
cargo test --test '*'

# Run only doc tests
cargo test --doc

# Run tests for a specific package in a workspace
cargo test -p package_name
```

### Coverage with cargo-llvm-cov

Preferred tool — uses LLVM instrumentation for accurate results.

```bash
# Install (once)
cargo install cargo-llvm-cov

# Run tests with coverage
cargo llvm-cov

# Generate HTML report
cargo llvm-cov --html
# Report at: target/llvm-cov/html/index.html

# Generate lcov format (for CI integration)
cargo llvm-cov --lcov --output-path lcov.info

# Fail if coverage below threshold
cargo llvm-cov --fail-under-lines {{COVERAGE_THRESHOLD}}

# Workspace coverage
cargo llvm-cov --workspace
```

### Coverage with cargo-tarpaulin

Alternative — simpler setup, Linux-only.

```bash
# Install (once)
cargo install cargo-tarpaulin

# Run with coverage
cargo tarpaulin

# HTML report
cargo tarpaulin --out html

# Fail if below threshold
cargo tarpaulin --fail-under {{COVERAGE_THRESHOLD}}

# Exclude test code from coverage
cargo tarpaulin --ignore-tests
```

### Report Locations

| Tool | Format | Path |
|------|--------|------|
| cargo-llvm-cov | HTML | `target/llvm-cov/html/index.html` |
| cargo-llvm-cov | lcov | user-specified via `--output-path` |
| cargo-tarpaulin | HTML | `tarpaulin-report.html` |
| cargo-tarpaulin | JSON | `tarpaulin-report.json` |

### Test Organization

```
src/
  lib.rs          # Unit tests in #[cfg(test)] mod tests { } at bottom
  module.rs       # Same — tests co-located with code
tests/
  integration_*.rs  # Integration tests — each file is a separate crate
examples/
  example.rs      # Runnable examples, also tested via cargo test --examples
```

<!-- WHY: Rust's built-in test framework puts unit tests in the same file as the code. Integration tests go in tests/. This is idiomatic — don't fight it. -->

### SAST for Rust

```bash
# Clippy — includes security-relevant lints
cargo clippy -- -D warnings

# Audit dependencies for known vulnerabilities
cargo audit

# Deny specific categories of advisories
cargo audit --deny warnings
```

### Property-Based Testing

Use `proptest` for property-based tests:

```rust
// In Cargo.toml dev-dependencies:
// proptest = "1"

use proptest::prelude::*;

proptest! {
    #[test]
    fn test_roundtrip(input in ".*") {
        let encoded = encode(&input);
        let decoded = decode(&encoded);
        prop_assert_eq!(input, decoded);
    }
}
```
