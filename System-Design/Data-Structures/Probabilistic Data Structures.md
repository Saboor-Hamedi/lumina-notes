---
id: a1b2c3d4-1137-4000-8000-000000000137
title: Probabilistic Data Structures
language: markdown
tags: ''
selection: null
isPinned: false
timestamp: 1781500001137
---
# Probabilistic Data Structures

Probabilistic data structures trade exactness for memory efficiency and speed. They give approximate answers with quantifiable error bounds.

## Bloom Filter

Tests set membership. False positives possible; false negatives impossible.

```
Insert "apple":   hash1("apple") → bit[3]=1
                  hash2("apple") → bit[7]=1
                  hash3("apple") → bit[12]=1

Check "banana":   hash1("banana") → bit[3]=1 ✓
                  hash2("banana") → bit[9]=0 ✗ → Not present

Check "apple":    hash1("apple") → bit[3]=1 ✓
                  hash2("apple") → bit[7]=1 ✓
                  hash3("apple") → bit[12]=1 ✓ → Probably present
```

```python
from pybloom_live import BloomFilter

bf = BloomFilter(capacity=10000, error_rate=0.001)

bf.add("apple")
bf.add("banana")

"apple" in bf   # True
"grape" in bf   # False (probably)
```

| k hashes | m bits | n items | False positive rate |
|----------|--------|---------|--------------------|
| Optimal: k = (m/n) × ln(2) | — | — | p ≈ (1 - e^{-kn/m})^k |

**Use cases**: Cache filtering, spell checkers, malicious URL detection, Medium reading recommendations.

## Counting Bloom Filter

Extends Bloom Filter to support deletions:

```python
class CountingBloomFilter:
    def __init__(self, size, num_hashes):
        self.counters = [0] * size
        self.hashes = num_hashes

    def add(self, item):
        for h in self._hashes(item):
            self.counters[h] += 1

    def remove(self, item):
        for h in self._hashes(item):
            self.counters[h] -= 1

    def __contains__(self, item):
        return all(self.counters[h] > 0 for h in self._hashes(item))
```

## HyperLogLog

Estimates the number of unique elements with ~1% error using ~1.5 KB.

```python
from hyperloglog import HyperLogLog

hll = HyperLogLog(0.01)  # 1% error

for item in dataset:
    hll.add(item)

cardinality = len(hll)  # cardinality estimate
```

**How it works**: Observes the longest run of leading zeros in hash values. More unique items → higher probability of seeing long runs.

**Use cases**: Unique visitor count, distinct IP addresses, cardinality estimation.

## MinHash (Min-wise Hashing)

Estimates Jaccard similarity between sets:

```
J(A, B) = |A ∩ B| / |A ∪ B|
```

```python
import hashlib

def minhash_signature(set_a, num_hashes=100):
    sig = [float('inf')] * num_hashes
    for element in set_a:
        for i in range(num_hashes):
            h = hashlib.sha256(f"{element}{i}".encode()).hexdigest()
            sig[i] = min(sig[i], int(h, 16))
    return sig

def jaccard_estimate(sig_a, sig_b):
    return sum(1 for a, b in zip(sig_a, sig_b) if a == b) / len(sig_a)
```

**Use cases**: Near-duplicate detection, document clustering, recommendation systems.

## Count-Min Sketch

Estimates frequency of elements in a stream.

```python
class CountMinSketch:
    def __init__(self, width, depth):
        self.counters = [[0] * width for _ in range(depth)]
        self.hash_seeds = [random.randint(0, 2**31) for _ in range(depth)]

    def add(self, item, count=1):
        for d in range(self.depth):
            idx = self._hash(item, self.hash_seeds[d]) % self.width
            self.counters[d][idx] += count

    def estimate(self, item):
        return min(
            self.counters[d][self._hash(item, self.hash_seeds[d]) % self.width]
            for d in range(self.depth)
        )
```

**Use cases**: Heavy hitters detection, frequency estimation in data streams, network traffic monitoring.

## Comparison Table

| Structure | Memory | Error Type | Error Rate | Supports Deletion |
|-----------|--------|------------|------------|-------------------|
| Bloom Filter | ~1 byte/item | False positive | Configurable | No* |
| Counting Bloom | ~4 bytes/item | False positive | Configurable | Yes |
| HyperLogLog | ~1.5 KB | Cardinality | ~1% | No |
| MinHash | ~100 KB/1M items | Similarity | ~5% | No |
| Count-Min Sketch | ~10 KB | Frequency | Overestimates | No |

\* Counting Bloom Filter supports deletion.

## Use Cases in Practice

| Use Case | Structure | Why |
|----------|-----------|-----|
| Medium "recommended" | Bloom Filter | 99% cache hit, no false negatives |
| Redis cluster | Bloom Filter | Avoid cache penetration |
| Google BigQuery | HyperLogLog | APPROX_COUNT_DISTINCT |
| PostgreSQL | HyperLogLog | extension for approximate counting |
| Apache Spark | MinHash | Near-duplicate document detection |
| Network monitoring | Count-Min Sketch | Detect top talkers |

**Links**: [[Data Structures Overview]] | [[Algorithm Paradigms]] | [[Stream Processing]] | [[Database Indexing Strategies]] | [[Time Series Analysis]]
