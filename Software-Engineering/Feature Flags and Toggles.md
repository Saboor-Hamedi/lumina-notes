---
id: a1b2c3d4-1145-4000-8000-000000000145
title: Feature Flags and Toggles
language: markdown
tags: [software-engineering]
selection: null
isPinned: false
timestamp: 1781500001145
---
# Feature Flags and Toggles

Feature flags (feature toggles) allow enabling/disabling functionality at runtime without deploying new code. They decouple deployment from release.

## Types of Feature Flags

| Type | Duration | Purpose |
|------|----------|---------|
| Release Toggle | Short-lived | Control rollout of new features |
| Experiment Toggle | Medium-lived | A/B testing, canary releases |
| Ops Toggle | Long-lived | Operational controls (circuit breakers) |
| Permission Toggle | Permanent | Feature access by user tier |

## Basic Implementation

```javascript
// Simple toggle (config file)
const features = {
    newCheckout: process.env.FEATURE_NEW_CHECKOUT === "true",
    darkMode: process.env.FEATURE_DARK_MODE === "true",
};

// In code
if (features.newCheckout) {
    renderNewCheckout();
} else {
    renderOldCheckout();
}
```

## Feature Flag Service

```javascript
class FeatureFlagService {
    constructor() {
        this.flags = new Map();
        this.client = new Redis();  // Store flags in Redis
    }

    async isEnabled(flagName, userId, context = {}) {
        const flag = await this.getFlag(flagName);
        if (!flag) return false;

        // Global override
        if (flag.enabledForAll) return true;
        if (flag.disabledForAll) return false;

        // User targeting
        if (flag.userIds?.includes(userId)) return true;

        // Percentage rollout
        if (flag.rolloutPercentage > 0) {
            const hash = this.hash(userId, flagName);
            return hash < flag.rolloutPercentage;
        }

        // Targeting rules
        return this.evaluateRules(flag, context);
    }
}
```

## Targeting Rules

```javascript
const flags = {
    newDashboard: {
        enabledForAll: false,
        rolloutPercentage: 0,
        rules: [
            { type: "beta", condition: (user) => user.tags?.includes("beta") },
            { type: "attribute", field: "plan", operator: "in", values: ["enterprise", "team"] },
        ]
    }
};
```

## Canary Release with Flags

```
Phase 1: Enable for internal users (1%)
Phase 2: Enable for beta users (5%)
Phase 3: Gradual rollout (25% → 50% → 75%)
Phase 4: Enable for all (100%)
Phase 5: Remove flag, clean up old code
```

## Flag Lifecycle

```yaml
# flag-definition.yaml
name: new-checkout
type: release
owner: checkout-team
created: 2026-01-15
rollout:
  - date: 2026-01-20  stage: internal     %: 1
  - date: 2026-01-25  stage: beta         %: 10
  - date: 2026-02-01  stage: public       %: 50
  - date: 2026-02-05  stage: full         %: 100
removal:
  target: 2026-03-01  # Remove old code after full rollout
```

## Kill Switch Pattern

```javascript
// Ops toggle: disable a feature in production without deploy
const cacheKillSwitch = {
    name: "disable-cache",
    type: "ops",
    description: "Emergency disable cache if Redis is failing",
};

app.get("/api/data", async (req, res) => {
    if (await features.isEnabled("disable-cache", req.user.id)) {
        return getDirectlyFromDB();  // Bypass cache
    }
    return getFromCache();
});
```

## Technical Implementation

```javascript
// Middleware pattern
function featureFlag(flagName, enabledHandler, disabledHandler) {
    return async (req, res, next) => {
        if (await features.isEnabled(flagName, req.user?.id)) {
            return enabledHandler(req, res, next);
        }
        return disabledHandler(req, res, next);
    };
}

app.get("/checkout",
    featureFlag("new-checkout",
        renderNewCheckout,
        renderOldCheckout
    )
);
```

## Client-Side Flags

```javascript
// Deliver flags to frontend via API
app.get("/api/features", async (req, res) => {
    const userFlags = {};
    for (const [name, flag] of flags) {
        userFlags[name] = await features.isEnabled(name, req.user.id);
    }
    res.json(userFlags);
});

// Frontend usage
const features = await fetch("/api/features").then(r => r.json());
if (features.newDashboard) {
    // Render new
}
```

## Flag Evaluation Context

```javascript
const context = {
    user: { id: "123", plan: "enterprise", beta: true },
    request: { path: "/api/v3", method: "POST" },
    environment: "production",
    date: new Date(),
    version: "3.2.1"
};
```

## Anti-Patterns

| Anti-Pattern | Issue | Fix |
|-------------|-------|-----|
| Nested flags | `if(a) { if(b) { if(c) ... } }` — impossible to test | Combine or simplify |
| Dead flags | Old flags never removed | Set removal deadlines |
| Flagging in loops | `for(x of list) { if(flag) ... }` — performance | Evaluate once before loop |
| Too many flags | Thousands of flags, no cleanup | Flag inventory audits |
| No default | Uninitialized flag crashes | Always default to off |

## Tools

| Tool | Platform | Features |
|------|----------|----------|
| LaunchDarkly | SaaS | Full-featured, targeting, experiments |
| Unleash | Open source | Self-hosted, SDKs |
| Flagsmith | Open source | Self-hosted or cloud |
| Split.io | SaaS | Feature flags + experimentation |
| GrowthBook | Open source | Feature flags + A/B testing |
| ConfigCat | SaaS | Simple feature flags |

## Cleanup Checklist

```
[x] Flag removed from code
[x] Old code path deleted
[x] All references to flag removed
[x] Flag definition deleted from service
[x] Documentation updated
[x] Tests updated (remove old path tests)
```

**Links**: [[Continuous Integration]] | [[GitLab CI]] | [[A-B Testing]] | [[Safe Deployments]] | [[Incident Response]]
