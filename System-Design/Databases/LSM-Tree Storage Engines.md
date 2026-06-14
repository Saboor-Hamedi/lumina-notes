---
id: a1b2c3d4-1140-4000-8000-000000000140
title: LSM-Tree Storage Engines
language: markdown
tags: [system-design, databases, lsm-tree, storage-engine]
selection: null
isPinned: false
timestamp: 1781500001140
---

**Links**: [[Database Indexing Deep Dive]] | [[Database Indexing Strategies]] | [[Cassandra]] | [[Database Engines Compared]] | [[Redis Deep Dive]] | [[Consistent Hashing]]


# LSM-Tree Storage Engines

LSM-Trees (Log-Structured Merge-Trees) are the storage engine behind many modern databases including LevelDB, RocksDB, Cassandra, ScyllaDB, and Bigtable.

## Architecture

```
Write ──► MemTable (in-memory, sorted)
              │
              │ (flush when full)
              ▼
         SSTable 0 (Level 0)
              │
              │ (compaction)
              ▼
         SSTable 1 (Level 1)
              │
              │ (compaction)
              ▼
         SSTable 2 (Level 2)
              │
              │ ...
```

## Core Components

| Component | Description |
|-----------|-------------|
| MemTable | In-memory sorted structure (RB-tree, skiplist) |
| Immutable MemTable | Read-only MemTable being flushed |
| SSTable | Sorted String Table (on-disk, immutable) |
| WAL | Write-Ahead Log for crash recovery |
| Compactor | Background thread merging SSTables |
| Bloom Filter | Optional, speeds up point lookups |

## Write Path

```python
class LSMTree:
    def __init__(self):
        self.memtable = SkipList()     # In-memory sorted structure
        self.wal = WriteAheadLog()     # Crash recovery
        self.levels = []               # SSTable levels
        self.memtable_size = 0

    def put(self, key, value):
        # 1. Write to WAL first (for durability)
        self.wal.append(key, value)

        # 2. Write to MemTable
        self.memtable.insert(key, value)
        self.memtable_size += len(key) + len(value)

        # 3. Flush if MemTable is full
        if self.memtable_size > MAX_MEMTABLE_SIZE:
            self.flush()

    def flush(self):
        # Freeze current MemTable, create new one
        immutable = self.memtable
        self.memtable = SkipList()
        self.memtable_size = 0
        self.wal.rotate()

        # Write to disk as SSTable
        sstable = SSTable.from_skiplist(immutable)
        self.levels[0].append(sstable)

        # Trigger compaction
        self.maybe_compact()
```

## Read Path

```python
    def get(self, key):
        # 1. Check active MemTable
        value = self.memtable.get(key)
        if value is not None:
            return value

        # 2. Check immutable MemTable
        if self.immutable:
            value = self.immutable.get(key)
            if value is not None:
                return value

        # 3. Search SSTables (newest level first)
        for level in self.levels:
            for sstable in level:
                # Optional: Bloom filter check
                if sstable.bloom_filter and key not in sstable.bloom_filter:
                    continue
                value = sstable.get(key)
                if value is not None:
                    return value

        return None  # Not found
```

## Compaction Strategies

### Size-Tiered Compaction (Cassandra)

```
Size-tiered: Merge SSTables of similar size
L0: [A] [B] [C] [D]     (many small files)
          ↓
L1: [A+B+C+D]            (one larger file)
```

### Leveled Compaction (RocksDB, LevelDB)

```
Leveled: Split into levels with exponentially increasing size

L0: [A] [B] [C]          (sorted runs, can overlap)
L1: [──sorted──]         (10x size of L0)
L2: [────sorted────]     (10x size of L1)
```

| Strategy | Write Amplification | Space Amplification | Read Amplification |
|----------|--------------------|--------------------|--------------------|
| Size-tiered | Low (~5x) | High (~20x) | High |
| Leveled | High (~15x) | Low (~1.1x) | Low |

## Write Amplification

```
Write amplification = actual bytes written / logical bytes written

Example:
  User writes 1 KB → compaction rewrites 15 KB → WA = 15x

Impact: Reduces SSD lifespan, increases CPU usage
```

## LSM vs B-Tree

| Aspect | LSM-Tree | B-Tree |
|--------|----------|--------|
| Write throughput | Higher (sequential writes) | Lower (random writes) |
| Read throughput | Lower (check multiple levels) | Higher (single page) |
| Space amplification | Higher (duplicates) | Lower |
| Write amplification | Higher (compaction) | Lower |
| Range scans | Good | Good |
| Point lookups | Fair (need Bloom filter) | Good |
| Storage | Writes faster on HDD/SSD | Writes slower on HDD |

## Bloom Filter Integration

```
Without Bloom filter: GET x → Check MemTable → Check L0 → L1 → L2 → ...
With Bloom filter:    GET x → Check MemTable → Check L0 → (skip L1, L2 if filter says "not present")
```

```python
class SSTable:
    def __init__(self, data, bloom_filter=None):
        self.data = data
        self.bloom_filter = bloom_filter or BloomFilter(capacity=len(data))

    def get(self, key):
        if self.bloom_filter and key not in self.bloom_filter:
            return None
        # Binary search on sorted data
        idx = self._binary_search(key)
        return self.data[idx].value if idx is not None else None
```

## Databases Using LSM-Trees

| Database | Engine | Notable Features |
|----------|--------|------------------|
| LevelDB | Google | Original LSM implementation |
| RocksDB | Facebook (Meta) | LevelDB fork, optimized for SSDs |
| Cassandra | Apache | Size-tiered + leveled compaction |
| ScyllaDB | Seastar | C++ reimplementation, zero-GC |
| Bigtable | Google | Original LSM concept |
| HBase | Apache | Hadoop-based LSM |
| SQLite4 LSM | — | Experimental LSM engine |
| TiKV | PingCAP | RocksDB-based distributed KV |

**Links**: [[Database Indexing Strategies]] | [[B-Tree Dynamics]] | [[SQL vs NoSQL Databases]] | [[Performance Profiling]] | [[System Design Fundamentals]]
