---
id: a1b2c3d4-1129-4000-8000-000000000129
title: Snapshot Testing
language: markdown
tags: ''
selection: null
isPinned: false
timestamp: 1781500001129
---

**Links**: [[Visual Regression Testing]] | [[Integration Testing Patterns]] | [[Unit Testing Guide]] | [[Software Testing Pyramid]] | [[Test-Driven Development]] | [[API Testing]]


# Snapshot Testing

Snapshot testing captures the output of a component or function and compares future runs against the stored snapshot. It catches unexpected changes by diffing against the reference.

## How Snapshots Work

```
First run: Generate output → Save to file (snapshot) → PASS
Second run: Generate output → Compare with saved snapshot
   ↓
Match → PASS
Mismatch → FAIL (or update snapshot)
```

## Snapshot Testing with Jest

```javascript
// Component snapshot
test("Button renders correctly", () => {
    const tree = renderer
        .create(<Button label="Click me" variant="primary" />)
        .toJSON();
    expect(tree).toMatchSnapshot();
});
```

Stored snapshot:

```javascript
// __snapshots__/Button.test.js.snap
exports[`Button renders correctly 1`] = `
<button
  className="btn btn-primary"
  onClick={[Function]}
>
  Click me
</button>
`;
```

## When Snapshots Change

```bash
# Snapshot changed → inspect the diff
# Intentional? → Update: jest --updateSnapshot
# Unintentional? → Fix the code

# CI should NOT update snapshots
jest --ci  # fails if snapshots need updating
```

## Best Practices

| Do | Don't |
|----|-------|
| Keep snapshots small/focused | Snapshot entire pages |
| Review snapshot diffs in PRs | Auto-update without review |
| Use with UI components | Use for business logic |
| Commit snapshots (they're source of truth) | Ignore snapshot files |
| Use descriptive test names for context | Keep generic names |

## Snapshot Testing for API Responses

```javascript
test("API returns user profile", async () => {
    const response = await fetch("/api/users/123");
    const data = await response.json();
    expect(data).toMatchSnapshot({
        created_at: expect.any(String),
        id: expect.any(Number),
    });
});
```

Inline snapshots (for smaller outputs):

```javascript
test("formats currency correctly", () => {
    expect(formatCurrency(10.5)).toMatchInlineSnapshot(`"$10.50"`);
});
```

## Snapshot Testing in Other Languages

```python
# pytest-snapshot
def test_user_json(snapshot):
    user = User(name="Alice", age=30)
    snapshot.assert_match(user.to_json(), "user_output.json")

# Snapshot stored in snapshots/test_file/user_output.json
```

## When to Use Snapshots

| Good For | Bad For |
|----------|---------|
| UI component rendering | Complex business logic |
| API response structure | Dynamically generated content |
| Configuration files | Random/nondeterministic output |
| Error messages | Very large outputs |
| Serialization formats | Frequently changing UI (churn) |

## Snapshot + Unit Tests

Snapshots complement rather than replace unit tests:

```javascript
test("Button renders with all variants", () => {
    // Snapshot catches visual changes
    const tree = renderer.create(<Button primary />).toJSON();
    expect(tree).toMatchSnapshot();
});

test("Button fires onClick", () => {
    // Unit test catches behavioral changes
    const onClick = jest.fn();
    render(<Button onClick={onClick} />);
    fireEvent.click(screen.getByRole("button"));
    expect(onClick).toHaveBeenCalledTimes(1);
});
```

## CI Integration

```yaml
# CI should fail on snapshot changes
test:
  script:
    - jest --ci  # fails if snapshots need updating
  artifacts:
    paths:
      - __snapshots__/  # include for debugging
```

## Updating Snapshots

```bash
# Update all snapshots
jest --updateSnapshot

# Update specific test
jest --testNamePattern="Button renders" --updateSnapshot
```

Always review snapshot diffs carefully — a changed snapshot might hide a bug.

**Links**: [[Unit Testing Guide]] | [[Software Testing Pyramid]] | [[Integration Testing Patterns]] | [[Property-Based Testing]] | [[Testing Tools]]
