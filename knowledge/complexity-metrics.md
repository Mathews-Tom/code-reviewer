# Complexity Metrics Reference

Measurement methods and thresholds for code complexity analysis.

## Cyclomatic Complexity (CC)

### Definition
Number of linearly independent paths through a function's control flow graph.

### Calculation
```
CC = E - N + 2P
```
Where: E = edges, N = nodes, P = connected components (usually 1 for a single function).

### Simplified Counting
Start at 1, add 1 for each:
- `if`, `elif`, `else if`
- `for`, `while`, `do-while`
- `case` (each case in switch)
- `catch`, `except`
- `&&`, `||` (short-circuit boolean operators)
- `?:` (ternary operator)

### Thresholds

| CC Value | Risk Level | Action |
|----------|-----------|--------|
| 1-5 | Low | No action needed |
| 6-10 | Moderate | Review for possible extraction |
| 11-20 | High | Refactor — extract sub-functions |
| 21-50 | Very High | Must refactor before merge |
| 50+ | Untestable | Block — rewrite required |

## Cognitive Complexity

### Definition
Measures how difficult code is to understand (vs. CC which measures testability).

### Increments
- +1 for each `if`, `else if`, `else`, `switch`, `for`, `while`, `catch`
- +1 for each nesting level (nested `if` inside `for` = +2 total)
- +1 for `break`/`continue` to label
- +1 for sequences of logical operators that mix `&&` and `||`

### Key Difference from CC
```python
# CC = 4, Cognitive = 7 (nesting adds cognitive load)
def process(data):
    if data:                          # +1 (CC), +1 (cognitive)
        for item in data:             # +1 (CC), +2 (cognitive: +1 struct, +1 nesting)
            if item.valid:            # +1 (CC), +3 (cognitive: +1 struct, +2 nesting)
                if item.ready:        # +1 (CC), +4 (cognitive: +1 struct, +3 nesting)
                    process(item)
```

### Thresholds

| Cognitive Complexity | Assessment |
|---------------------|------------|
| 0-8 | Clear, easy to understand |
| 9-15 | Starting to require careful reading |
| 16-25 | Difficult to follow, needs simplification |
| 25+ | Must refactor |

## Function Length

| Lines | Assessment | Severity |
|-------|-----------|----------|
| 1-20 | Ideal | — |
| 21-50 | Acceptable | — |
| 51-100 | Long, review for extraction | MEDIUM |
| 101-200 | Too long | HIGH |
| 200+ | Must split | HIGH |

## Parameter Count

| Count | Assessment | Severity |
|-------|-----------|----------|
| 0-3 | Ideal | — |
| 4-5 | Acceptable | — |
| 6-7 | Introduce parameter object | MEDIUM |
| 8+ | Must refactor | HIGH |

### Remediation: Parameter Object
```python
# Before: 7 parameters
def create_user(name, email, phone, address, city, state, zip_code): ...

# After: parameter object
@dataclass
class UserInfo:
    name: str
    email: str
    phone: str
    address: str
    city: str
    state: str
    zip_code: str

def create_user(info: UserInfo): ...
```

## Nesting Depth

| Depth | Assessment | Severity |
|-------|-----------|----------|
| 1-2 | Normal | — |
| 3 | Watch | — |
| 4 | Refactor recommended | MEDIUM |
| 5+ | Must refactor | HIGH |

### Reduction Techniques
- Early return / guard clause
- Extract nested block into named function
- Replace conditional with polymorphism
- Use lookup tables instead of nested switch/if

## Return Points

| Count | Assessment |
|-------|-----------|
| 1 | Single exit — traditional |
| 2-3 | Guard clauses + main return — ideal |
| 4-5 | Review — may indicate complex logic |
| 6+ | Refactor — too many paths |

## File-Level Metrics

| Metric | Threshold | Severity |
|--------|-----------|----------|
| Lines per file | > 500 | MEDIUM |
| Functions per file | > 20 | MEDIUM |
| Classes per file | > 3 | MEDIUM |
| Imports | > 15 | LOW (possible god module) |
