<!-- tier: project -->
<!-- requires: testing-universal -->

## Go Testing — Stack-Specific Commands

### Running Tests

```bash
# Run all tests
go test ./...

# Run tests with verbose output
go test -v ./...

# Run a specific test by name
go test -run TestFunctionName ./...

# Run tests for a specific package
go test ./pkg/auth/...

# Run tests with race detector
go test -race ./...

# Run short tests only (skip long-running integration tests)
go test -short ./...
```

### Coverage

```bash
# Run tests with coverage profile
go test -coverprofile=coverage.out ./...

# View coverage summary in terminal
go tool cover -func=coverage.out

# Generate HTML coverage report
go tool cover -html=coverage.out -o coverage.html

# Fail if coverage below threshold (use in CI)
# Go doesn't have a built-in threshold flag — use a script:
go test -coverprofile=coverage.out ./... && \
  go tool cover -func=coverage.out | grep total | \
  awk '{if ($3+0 < {{COVERAGE_THRESHOLD}}) { print "Coverage " $3 " below {{COVERAGE_THRESHOLD}}%"; exit 1 }}'
```

<!-- WHY: Go's built-in coverage tooling is minimal but sufficient. The awk one-liner is ugly but avoids adding a dependency just for threshold enforcement. If you want something cleaner, use a CI-level check. -->

### Report Locations

| Format | Path | Use |
|--------|------|-----|
| Profile | `coverage.out` | Machine-readable, input to other tools |
| HTML | `coverage.html` | Human review |
| Function summary | stdout from `go tool cover -func` | Quick terminal check |

### Test Organization

```
pkg/
  auth/
    auth.go
    auth_test.go           # Unit tests — same package, same directory
  store/
    store.go
    store_test.go
    store_integration_test.go  # Integration tests — use build tag
test/
  e2e/
    api_test.go            # E2E tests — separate directory
```

<!-- WHY: Go convention is tests in the same package and directory as the code. Use _test.go suffix — the toolchain enforces this. For integration tests, use build tags to separate them from unit tests. -->

### Build Tags for Test Separation

```go
//go:build integration

package store_test

// Integration tests that need a real database go here
```

```bash
# Run only unit tests (default — skips tagged tests)
go test ./...

# Run integration tests explicitly
go test -tags=integration ./...

# Run all tests
go test -tags=integration ./...
```

### Table-Driven Tests

Use table-driven tests — this is idiomatic Go:

```go
func TestAdd(t *testing.T) {
    tests := []struct {
        name     string
        a, b     int
        expected int
    }{
        {"positive", 1, 2, 3},
        {"zero", 0, 0, 0},
        {"negative", -1, -2, -3},
    }

    for _, tt := range tests {
        t.Run(tt.name, func(t *testing.T) {
            got := Add(tt.a, tt.b)
            if got != tt.expected {
                t.Errorf("Add(%d, %d) = %d, want %d", tt.a, tt.b, got, tt.expected)
            }
        })
    }
}
```

### SAST for Go

```bash
# Security-focused static analysis
gosec ./...

# General static analysis (includes many useful checks)
staticcheck ./...

# Vulnerability check for dependencies
govulncheck ./...
```

### Mocking

Use interfaces and generate mocks with **mockgen** or **moq**:

```bash
# Install mockgen
go install go.uber.org/mock/mockgen@latest

# Generate mock from interface
mockgen -source=pkg/store/store.go -destination=pkg/store/mock_store.go -package=store
```

<!-- WHY: Go's interfaces are implicit — any type that implements the methods satisfies the interface. Design for testability by accepting interfaces, not concrete types. -->
