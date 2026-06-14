---
id: a1b2c3d4-1164-4000-8000-000000000164
title: UUID vs Snowflake vs ULID
language: markdown
tags: ''
selection: null
isPinned: false
timestamp: 1781500001164
---

**Links**: [[Consistent Hashing]] | [[Database Sharding]] | [[Database Replication Strategies]] | [[Database Engines Compared]] | [[CRDTs]] | [[SQL vs NoSQL Databases]]


# UUID vs Snowflake vs ULID

Different ID generation strategies for distributed systems. The choice affects sortability, index performance, and collaboration.

## Comparison Table

| Property | UUIDv4 | UUIDv7 | Snowflake | ULID |
|----------|--------|--------|-----------|------|
| Size | 128 bits (36 chars) | 128 bits (36 chars) | 64 bits (19 digits) | 128 bits (26 chars) |
| Sortable | No | Yes (by timestamp) | Yes | Yes |
| Timestamp | No | Yes (ms) | Yes (ms) | Yes (ms) |
| Randomness | 122 bits | 62 bits | 12 bits | 80 bits |
| ID generation | Client or server | Client or server | Server only | Client or server |
| URL-safe | No (has dashes) | No (has dashes) | Yes | Yes (Crockford) |
| Collisions | ~2^71 needed for 50% | ~2^31 for same ms | None (coordinated) | ~2^39 for same ms |

## UUID Versions

```
UUID v4: random
  Format: xxxxxxxx-xxxx-4xxx-yxxx-xxxxxxxxxxxx
  Example: 550e8400-e29b-41d4-a716-446655440000

UUID v7: time-ordered (RFC 9562)
  Format: tttttttt-tttt-7xxx-yxxx-xxxxxxxxxxxx
  Example: 018f3a6e-1234-7b00-a000-000000000001
  - First 48 bits: Unix ms timestamp
  - Next 4 bits: version (7)
  - Next 12 bits: random
  - Next 2 bits: variant
  - Remaining 62 bits: random
```

```javascript
// UUID v7 generation (manual)
function uuidv7() {
    const timestamp = BigInt(Date.now());
    const random = crypto.getRandomValues(new Uint8Array(10));

    const bytes = new Uint8Array(16);
    // Timestamp (48 bits) + version 7
    bytes[0] = Number((timestamp >> 40n) & 0xFFn);
    bytes[1] = Number((timestamp >> 32n) & 0xFFn);
    bytes[2] = Number((timestamp >> 24n) & 0xFFn);
    bytes[3] = Number((timestamp >> 16n) & 0xFFn);
    bytes[4] = Number((timestamp >> 8n) & 0xFFn);
    bytes[5] = Number(timestamp & 0xFFn);
    bytes[6] = 0x70 | (random[0] & 0x0F);  // version 7
    bytes[8] = 0x80 | (random[1] & 0x3F);  // variant
    bytes[7] = random[2];
    bytes[9] = random[3];
    bytes[10] = random[4];
    bytes[11] = random[5];
    bytes[12] = random[6];
    bytes[13] = random[7];
    bytes[14] = random[8];
    bytes[15] = random[9];

    return bytesToHex(bytes);
}
```

## Snowflake (Twitter-style)

```
64-bit integer:
  0 0000000000 000000000000 00000000000000000000000
  │     │            │                │
  │     │            │                └── Sequence (12 bits)
  │     │            └── Worker ID (10 bits)
  │     └── Timestamp (41 bits, ms since epoch)
  └── Sign bit (unused)
```

```python
import time
import threading

class SnowflakeGenerator:
    def __init__(self, worker_id, datacenter_id=0, epoch=1700000000000):
        self.worker_id = worker_id & 0x3FF      # 10 bits
        self.datacenter_id = datacenter_id & 0x1F  # 5 bits
        self.epoch = epoch
        self.sequence = 0
        self.last_timestamp = -1
        self.lock = threading.Lock()

    def _current_millis(self):
        return int(time.time() * 1000)

    def generate(self):
        with self.lock:
            timestamp = self._current_millis()

            if timestamp < self.last_timestamp:
                raise Exception("Clock moved backwards")

            if timestamp == self.last_timestamp:
                self.sequence = (self.sequence + 1) & 0xFFF  # 12 bits
                if self.sequence == 0:
                    # Sequence exhausted, wait for next ms
                    while timestamp <= self.last_timestamp:
                        timestamp = self._current_millis()
            else:
                self.sequence = 0

            self.last_timestamp = timestamp

            id = ((timestamp - self.epoch) << 22) \
               | (self.datacenter_id << 17) \
               | (self.worker_id << 12) \
               | self.sequence

            return id
```

## ULID

```
26 characters, Crockford Base32:
  TTTTTTTTTT RRRRRRRRRRRRRRRR
  │          │
  │          └── Random (80 bits)
  └── Timestamp (48 bits, ms)
```

```python
import time
import os

def ulid():
    CROCKFORD = "0123456789ABCDEFGHJKMNPQRSTVWXYZ"
    timestamp = int(time.time() * 1000)

    # Encode timestamp (10 chars)
    ts_chars = []
    for i in range(10):
        ts_chars.append(CROCKFORD[timestamp & 0x1F])
        timestamp >>= 5
    ts_chars.reverse()

    # Encode random (16 chars)
    rand_bytes = os.urandom(10)
    rand_val = int.from_bytes(rand_bytes, "big")
    rand_chars = []
    for i in range(16):
        rand_chars.append(CROCKFORD[rand_val & 0x1F])
        rand_val >>= 5

    return "".join(ts_chars + rand_chars)
```

## B-tree Index Performance

| ID Type | Insert Pattern | Page Splits | Index Fragmentation |
|---------|---------------|-------------|---------------------|
| UUIDv4 | Random | High | High |
| UUIDv7 | Monotonic | Low | Low |
| Snowflake | Monotonic | Low | Low |
| ULID | Monotonic | Low | Low |
| Auto-increment | Monotonic | Minimal | Minimal |
| NanoID | Random | High | High |

## When to Use What

| Use Case | Recommended ID |
|----------|---------------|
| Public API identifiers | UUIDv7 or ULID |
| Distributed database records | Snowflake or ULID |
| Sequential primary keys | UUIDv7 (avoids sequential leak) |
| URL-safe identifiers | ULID or NanoID |
| Minimal storage (DB index) | Snowflake (8 bytes) |
| Client-generated without server | UUIDv7 or ULID |
| Log correlation IDs | UUIDv7 (time-ordered) |
| Short, user-friendly codes | NanoID |

## Unique Constraints

```sql
-- UUID as primary key
CREATE TABLE users (
    id UUID PRIMARY KEY DEFAULT gen_random_uuid(),  -- UUIDv4
    created_at TIMESTAMPTZ DEFAULT NOW()
);

-- UUIDv7 in PostgreSQL (extension)
CREATE EXTENSION pg_uuidv7;
CREATE TABLE orders (
    id UUID PRIMARY KEY DEFAULT uuidv7(),
    created_at TIMESTAMPTZ DEFAULT NOW()
);
```
