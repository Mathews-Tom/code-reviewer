# DRY Violations Reference

Detection criteria, severity classification, and remediation patterns for code duplication.

## Duplication Types

| Type | Description | Detection Method |
|------|-------------|-----------------|
| Exact clone | Identical code blocks | Textual comparison |
| Renamed clone | Same structure, different identifiers | Token comparison |
| Near-miss clone | Similar logic with small variations | AST comparison |
| Semantic clone | Different code, same behavior | Functional analysis |

## Detection Thresholds

| Duplication Size | Count | Severity | Action |
|-----------------|-------|----------|--------|
| 3-5 lines | 2 occurrences | LOW | Note, watch |
| 3-5 lines | 3+ occurrences | MEDIUM | Extract utility |
| 6-15 lines | 2 occurrences | MEDIUM | Extract function |
| 6-15 lines | 3+ occurrences | HIGH | Extract immediately |
| 16+ lines | 2+ occurrences | HIGH | Must extract |
| Any size | Diverged copies | HIGH | Single source of truth |

## Common Duplication Patterns

### 1. Copy-Paste Logic

```python
# DUPLICATED: same validation in two handlers
def create_user(request):
    if not request.email or "@" not in request.email:
        raise ValidationError("Invalid email")
    if len(request.password) < 8:
        raise ValidationError("Password too short")
    ...

def update_user(request):
    if not request.email or "@" not in request.email:
        raise ValidationError("Invalid email")
    if len(request.password) < 8:
        raise ValidationError("Password too short")
    ...

# FIX: extract validation
def validate_user_input(email: str, password: str) -> None:
    if not email or "@" not in email:
        raise ValidationError("Invalid email")
    if len(password) < 8:
        raise ValidationError("Password too short")
```

### 2. Magic Numbers/Strings

```python
# DUPLICATED: same values in multiple places
timeout = 30  # in module A
timeout = 30  # in module B
timeout = 30  # in module C

# FIX: single constant
# constants.py
DEFAULT_TIMEOUT_SECONDS = 30
```

### 3. Configuration Duplication

```yaml
# DUPLICATED: same DB config in multiple files
# docker-compose.yml
DB_HOST: postgres
DB_PORT: 5432

# app-config.yml
database:
  host: postgres
  port: 5432

# FIX: single source, reference via env vars or shared config
```

### 4. Repeated Query Patterns

```python
# DUPLICATED: same query structure
def get_active_users():
    return db.query(User).filter(User.active == True).filter(User.deleted_at == None).all()

def get_active_admins():
    return db.query(User).filter(User.active == True).filter(User.deleted_at == None).filter(User.role == "admin").all()

# FIX: base query method
def _active_users_query():
    return db.query(User).filter(User.active == True).filter(User.deleted_at == None)

def get_active_users():
    return _active_users_query().all()

def get_active_admins():
    return _active_users_query().filter(User.role == "admin").all()
```

### 5. Error Handling Boilerplate

```typescript
// DUPLICATED: same try/catch in every handler
app.get("/users", async (req, res) => {
  try {
    const users = await getUsers();
    res.json(users);
  } catch (error) {
    logger.error(error);
    res.status(500).json({ error: "Internal server error" });
  }
});

// FIX: error handling middleware / wrapper
const asyncHandler = (fn: RequestHandler) => (req, res, next) =>
  Promise.resolve(fn(req, res, next)).catch(next);

app.get("/users", asyncHandler(async (req, res) => {
  const users = await getUsers();
  res.json(users);
}));
```

## When NOT to Extract

| Situation | Reason |
|-----------|--------|
| Coincidental similarity | Code looks similar now but serves different purposes |
| Test setup code | Tests benefit from explicit, self-contained setup |
| Two occurrences of simple code | Extraction may add unnecessary indirection |
| Cross-boundary duplication | Coupling modules to eliminate small duplication is worse |
| Configuration for different environments | Environment-specific values are inherently different |

## Extraction Strategies

| Pattern | When to Use | Target |
|---------|-------------|--------|
| Extract function | Repeated logic within a module | Same module, private function |
| Extract utility | Repeated across modules | `utils/` or `common/` module |
| Extract constant | Magic numbers/strings | `constants` module |
| Extract base class | Shared behavior across classes | Base class or mixin |
| Extract middleware | Repeated request handling | Middleware function |
| Extract config | Values in multiple files | Single config file |
| Extract template | Repeated structural patterns | Generic/template function |

## Diverged Copy Detection

### Signal
Two code blocks that were clearly copied but have since diverged:
- Same structure, different edge case handling
- One copy has a bug fix the other lacks
- Variable names partially renamed

### Risk
Diverged copies are the most dangerous DRY violation — a fix in one copy does not propagate to the other. Flag as HIGH severity.
