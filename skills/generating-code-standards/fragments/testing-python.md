<!-- tier: project -->
<!-- requires: testing-universal -->

## Python Testing — Stack-Specific Commands

### Running Tests

```bash
# Run all tests
pytest

# Run with verbose output
pytest -v

# Run a specific test file
pytest tests/test_auth.py

# Run a specific test by name
pytest -k "test_login_success"

# Run tests matching a marker
pytest -m integration

# Stop on first failure
pytest -x

# Run in parallel (requires pytest-xdist)
pytest -n auto
```

### Coverage with coverage.py / pytest-cov

```bash
# Install
pip install pytest-cov

# Run tests with coverage
pytest --cov=src --cov-report=html --cov-report=term

# Fail if below threshold
pytest --cov=src --cov-fail-under={{COVERAGE_THRESHOLD}}

# Generate XML report (for CI integration)
pytest --cov=src --cov-report=xml

# Show per-file coverage in terminal
pytest --cov=src --cov-report=term-missing
```

### Coverage Configuration

Add to `pyproject.toml`:

```toml
[tool.coverage.run]
source = ["src"]
branch = true
omit = ["*/tests/*", "*/migrations/*"]

[tool.coverage.report]
fail_under = {{COVERAGE_THRESHOLD}}
show_missing = true
exclude_lines = [
    "pragma: no cover",
    "if TYPE_CHECKING:",
    "if __name__ == .__main__.",
]
```

### Report Locations

| Format | Path | Use |
|--------|------|-----|
| HTML | `htmlcov/index.html` | Human review |
| XML | `coverage.xml` | CI tool integration |
| Terminal | stdout | Quick check |

### Test Organization

```
src/
  auth/
    __init__.py
    service.py
  store/
    __init__.py
    repository.py
tests/
  unit/
    test_auth_service.py     # Unit tests
    test_store_repository.py
  integration/
    test_api.py              # Integration tests
    conftest.py              # Shared fixtures for integration
  conftest.py                # Global fixtures
```

<!-- WHY: Python convention is a separate tests/ directory mirroring the src/ structure. conftest.py files provide fixtures scoped to their directory — use them for setup/teardown instead of duplicating code. -->

### Markers for Test Separation

Define markers in `pyproject.toml`:

```toml
[tool.pytest.ini_options]
markers = [
    "integration: tests that require external services",
    "slow: tests that take more than 5 seconds",
]
```

```bash
# Run only unit tests (skip integration)
pytest -m "not integration"

# Run only integration tests
pytest -m integration
```

### Fixtures

Use pytest fixtures for setup/teardown:

```python
import pytest

@pytest.fixture
def db_session():
    session = create_test_session()
    yield session
    session.rollback()
    session.close()

def test_create_user(db_session):
    user = create_user(db_session, name="Test")
    assert user.id is not None
```

### SAST for Python

```bash
# Security-focused static analysis
bandit -r src/

# Multi-language SAST with community rules
semgrep --config=auto src/

# Dependency vulnerability check
pip-audit

# Alternative dependency check
safety check
```

### Type Checking

```bash
# Run mypy for static type checking
mypy src/

# Run pyright (alternative, faster)
pyright src/
```

Configure in `pyproject.toml`:

```toml
[tool.mypy]
strict = true
warn_return_any = true
warn_unused_configs = true
```

<!-- WHY: Type checking catches a class of bugs that tests don't — wrong argument types, missing return values, None where not expected. Treat mypy errors as build failures. -->

### Mocking

Use `unittest.mock` (stdlib) or `pytest-mock`:

```python
from unittest.mock import patch, MagicMock

def test_send_email(mocker):
    mock_smtp = mocker.patch("src.notifications.smtp_client")
    send_welcome_email("user@example.com")
    mock_smtp.send.assert_called_once()
```

<!-- WHY: Mock at boundaries (HTTP clients, database connections, external services), not internal functions. If you're mocking half the codebase, the design needs work. -->
