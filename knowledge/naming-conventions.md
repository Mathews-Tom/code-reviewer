# Naming Conventions Reference

Language-specific naming rules and universal naming principles.

## Universal Principles

| Principle | Good | Bad |
|-----------|------|-----|
| Descriptive | `user_count` | `n`, `cnt`, `x` |
| Pronounceable | `customer_address` | `cstmr_addr` |
| Searchable | `MAX_RETRIES = 3` | `3` (magic number) |
| No encoding | `name` | `str_name`, `s_name` |
| Verb for functions | `calculate_total()` | `total()` (ambiguous) |
| Noun for variables | `total_price` | `calculate` |
| Boolean prefix | `is_valid`, `has_items` | `valid`, `items` |

## Language-Specific Conventions

### Python (PEP 8)

| Element | Convention | Example |
|---------|-----------|---------|
| Variable | snake_case | `user_name` |
| Function | snake_case | `get_user_by_id()` |
| Class | PascalCase | `HttpClient` |
| Constant | UPPER_SNAKE | `MAX_CONNECTIONS` |
| Module | snake_case | `data_processor.py` |
| Package | lowercase (no underscores preferred) | `mypackage` |
| Private | leading underscore | `_internal_cache` |
| Name-mangled | double underscore | `__private_attr` |
| Type variable | PascalCase | `T`, `KeyType` |
| Protocol | PascalCase + -able/-ible | `Comparable` |

### JavaScript / TypeScript

| Element | Convention | Example |
|---------|-----------|---------|
| Variable | camelCase | `userName` |
| Function | camelCase | `getUserById()` |
| Class | PascalCase | `HttpClient` |
| Constant | UPPER_SNAKE or camelCase | `MAX_CONNECTIONS` |
| Interface | PascalCase (no I prefix) | `UserService` |
| Type | PascalCase | `RequestConfig` |
| Enum | PascalCase | `HttpStatus` |
| Enum member | PascalCase or UPPER_SNAKE | `NotFound` or `NOT_FOUND` |
| File (component) | PascalCase | `UserProfile.tsx` |
| File (utility) | camelCase or kebab-case | `formatDate.ts` |
| Private field | `#` prefix (ES2022) | `#cache` |

### Go

| Element | Convention | Example |
|---------|-----------|---------|
| Exported | PascalCase | `GetUser()`, `HttpClient` |
| Unexported | camelCase | `getUserFromDB()` |
| Package | lowercase, single word | `http`, `strconv` |
| Interface | PascalCase + -er suffix | `Reader`, `Stringer` |
| Acronyms | All caps | `HTTPClient`, `userID` |
| Error variable | `Err` prefix | `ErrNotFound` |
| Receiver | 1-2 char abbreviation | `func (s *Server)` |

### Java

| Element | Convention | Example |
|---------|-----------|---------|
| Variable | camelCase | `userName` |
| Method | camelCase | `getUserById()` |
| Class | PascalCase | `HttpClient` |
| Interface | PascalCase | `UserService` |
| Constant | UPPER_SNAKE | `MAX_CONNECTIONS` |
| Package | lowercase dot-separated | `com.example.auth` |
| Enum | PascalCase (members UPPER_SNAKE) | `Status.NOT_FOUND` |
| Generic type | Single uppercase letter | `T`, `E`, `K`, `V` |

## Common Anti-Patterns

| Anti-Pattern | Example | Fix |
|-------------|---------|-----|
| Single-letter names | `x`, `d`, `t` | `index`, `data`, `timestamp` |
| Abbreviations | `usr_mgr`, `btn_clk` | `user_manager`, `button_click` |
| Type in name | `user_list`, `name_string` | `users`, `name` |
| Negative booleans | `not_disabled` | `enabled` |
| Generic names | `data`, `info`, `temp`, `result` | Context-specific names |
| Numbered names | `data1`, `data2` | Descriptive differentiation |
| Misleading names | `get_user()` that creates users | `create_user()` |
| Hungarian notation | `strName`, `iCount` | `name`, `count` |

## Function Naming Patterns

| Action | Verb | Example |
|--------|------|---------|
| Retrieve | `get`, `fetch`, `find`, `load` | `get_user()`, `find_by_email()` |
| Create | `create`, `build`, `make`, `new` | `create_order()` |
| Update | `update`, `set`, `modify` | `update_profile()` |
| Delete | `delete`, `remove`, `destroy` | `delete_session()` |
| Check | `is`, `has`, `can`, `should` | `is_valid()`, `has_permission()` |
| Convert | `to`, `from`, `parse`, `format` | `to_json()`, `parse_date()` |
| Compute | `calculate`, `compute`, `derive` | `calculate_total()` |
| Validate | `validate`, `check`, `verify` | `validate_email()` |

## Constants vs Magic Numbers

### Must Be Named Constants
- Configuration values (timeouts, limits, thresholds)
- Array indices with semantic meaning
- Bit flags and masks
- HTTP status codes used in comparisons
- Regular expression patterns

### Acceptable Inline
- `0`, `1`, `-1` in obvious arithmetic
- `""` (empty string) for initialization
- `True`/`False`/`None`/`null`
- Array index `0` for first element
