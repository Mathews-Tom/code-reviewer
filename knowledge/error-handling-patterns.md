# Error Handling Patterns Reference

Anti-patterns to detect and correct patterns for error handling across languages.

## Anti-Patterns (Flag These)

### Silent Failures

| Pattern | Language | Severity |
|---------|----------|----------|
| `except: pass` | Python | HIGH |
| `except Exception: pass` | Python | HIGH |
| `catch (e) {}` | JavaScript | HIGH |
| `catch (Exception e) { }` | Java | HIGH |
| `if err != nil { return nil }` (no log) | Go | MEDIUM |
| `_ = riskyOperation()` | Go | MEDIUM |

### Bare Catch-All

| Pattern | Language | Severity |
|---------|----------|----------|
| `except:` (no type) | Python | MEDIUM |
| `catch { }` (no variable) | JavaScript | MEDIUM |
| `catch (Throwable t)` | Java | MEDIUM |
| `rescue` (no class) | Ruby | MEDIUM |

### Generic Error Messages

```python
# BAD: no context
raise ValueError("invalid input")

# GOOD: specific context
raise ValueError(f"user_id must be positive integer, got {user_id!r}")
```

### Error Swallowing with Logging Only

```python
# BAD: logs but continues as if nothing happened
try:
    process_payment(order)
except PaymentError as e:
    logger.error(f"Payment failed: {e}")
    # Continues execution — order marked as processed without payment
```

## Correct Patterns

### Python

```python
# Specific exception types
try:
    result = parse_config(path)
except FileNotFoundError:
    raise ConfigError(f"Config file not found: {path}") from None
except json.JSONDecodeError as e:
    raise ConfigError(f"Invalid JSON in {path}: {e}") from e

# Context manager for cleanup
with open(path) as f:
    data = f.read()

# Custom exceptions with context
class OrderError(Exception):
    def __init__(self, order_id: str, reason: str) -> None:
        super().__init__(f"Order {order_id}: {reason}")
        self.order_id = order_id
```

### JavaScript / TypeScript

```typescript
// Typed error handling
try {
  const data = await fetchUser(userId);
} catch (error) {
  if (error instanceof NotFoundError) {
    return res.status(404).json({ error: "User not found" });
  }
  if (error instanceof ValidationError) {
    return res.status(400).json({ error: error.message });
  }
  throw error; // Re-throw unexpected errors
}
```

### Go

```go
// Wrap errors with context
result, err := db.Query(query, args...)
if err != nil {
    return fmt.Errorf("querying users by email %s: %w", email, err)
}

// Sentinel errors for comparison
var ErrNotFound = errors.New("not found")

if errors.Is(err, ErrNotFound) {
    // handle not found
}
```

## Error Handling Checklist

### I/O Operations (must have error handling)
- File read/write operations
- Network requests (HTTP, database, message queue)
- Process execution (subprocess, child_process)
- JSON/XML parsing of external data
- Environment variable access (missing vars)

### Resource Cleanup
- Database connections must be closed in finally/defer
- File handles must be closed (use context managers/defer)
- HTTP response bodies must be closed (Go)
- Temporary files must be cleaned up

## Error Propagation Rules

| Situation | Action |
|-----------|--------|
| Can handle meaningfully | Catch, handle, recover |
| Can add context | Catch, wrap with context, re-throw |
| Cannot handle | Let it propagate (do not catch) |
| Boundary (API, CLI) | Catch, translate to user-facing error |
| Logging needed | Log at the handling boundary, not at every level |

## Detection Signals by Severity

### CRITICAL
- No error handling on database operations
- No error handling on authentication flows
- Exception caught but operation continues as if successful
- Payment/financial operations without error handling

### HIGH
- Silent `except: pass` or `catch {}` blocks
- Missing null/undefined checks before dereferencing
- No error handling on file I/O
- Generic error messages in user-facing APIs

### MEDIUM
- Bare except without specific type
- Catching Exception/Throwable instead of specific types
- Error logged but not returned/raised
- Missing validation on external input

### LOW
- Inconsistent error message format
- Missing error type documentation
- Overly broad catch when narrower is possible
