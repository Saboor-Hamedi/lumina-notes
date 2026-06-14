---
id: a1b2c3d4-1138-4000-8000-000000000138
title: Raft Consensus Algorithm
language: markdown
tags: ''
selection: null
isPinned: false
timestamp: 1781500001138
---
# Raft Consensus Algorithm

Raft is a consensus algorithm designed as a more understandable alternative to Paxos. It ensures that multiple servers agree on a shared state even when some fail.

## Key Properties

- **Fault tolerance**: Survives N-1 node failures out of 2N+1 nodes
- **Safety**: Never returns incorrect results
- **Liveness**: Eventually produces a result
- **Understandability**: Decomposed into leader election, log replication, and safety

## Server States

```
                    ┌──────────┐
                    │  Follower │◄────────┐
                    └─────┬────┘          │
                          │ election       │
                          │ timeout        │
                    ┌─────▼────┐          │
                    │ Candidate │          │
                    └─────┬────┘          │
                          │ wins          │
                          │ election       │
                    ┌─────▼────┐          │
                    │  Leader  ├──────────┘
                    └──────────┘  (heartbeat)
```

| State | Role |
|-------|------|
| Follower | Passive, accepts log entries from leader |
| Candidate | Initiates election, requests votes |
| Leader | Handles all client requests, replicates log |

## Leader Election

```
Each follower has a random election timeout (150-300ms).

1. Follower receives no heartbeat → timeout → becomes Candidate
2. Candidate increments term, votes for self, sends RequestVote RPCs
3. If majority votes → becomes Leader
4. Leader sends heartbeats → resets followers' timers
5. If another server is leader → step down to Follower
```

```python
class RaftNode:
    def __init__(self, node_id, peers):
        self.state = "follower"
        self.current_term = 0
        self.voted_for = None
        self.election_timeout = random.uniform(150, 300)  # ms
        self.last_heartbeat = time.time()

    def check_election_timeout(self):
        if time.time() - self.last_heartbeat > self.election_timeout:
            self.start_election()

    def start_election(self):
        self.state = "candidate"
        self.current_term += 1
        self.voted_for = self.node_id
        votes = 1
        for peer in self.peers:
            if peer.request_vote(self.current_term, self.node_id):
                votes += 1
        if votes > len(self.peers) // 2:
            self.state = "leader"
            self.send_heartbeats()
```

## Log Replication

```
Client → Leader: SET x=3

1. Leader appends entry to its log
2. Leader sends AppendEntries RPC to all followers
3. Followers append to their logs
4. Followers respond with success
5. Leader commits once majority acknowledges
6. Leader applies entry to state machine
7. Leader notifies followers of commit index
```

```python
class LogEntry:
    def __init__(self, term, command):
        self.term = term
        self.command = command

def replicate(self, command):
    entry = LogEntry(self.current_term, command)
    self.log.append(entry)

    acks = 1  # self
    for peer in self.peers:
        if peer.append_entries(self.current_term, self.node_id, self.log, self.commit_index):
            acks += 1

    if acks > len(self.peers) // 2:
        self.commit_index = len(self.log) - 1
        self.apply_state_machine()
```

## Safety Properties

| Property | Description |
|----------|-------------|
| Election Safety | At most one leader per term |
| Leader Append-Only | Leader never overwrites/deletes log entries |
| Log Matching | If two logs have same index/term, entries are identical |
| Leader Completeness | Committed entries are present in all future leaders |
| State Machine Safety | Servers apply same commands in same order |

## Log Consistency Check

```
Before appending, the leader checks:
- The follower's log has an entry matching (prevLogIndex, prevLogTerm)
- If not, decrement index and retry
```

## Cluster Membership Changes

Use a **joint consensus** transition (C_old,new):

```
Configuration: C_old → C_old,new (joint) → C_new

During joint consensus:
- Leaders must get majority from BOTH old and new configurations
- Servers use either config
- Safety maintained throughout transition
```

## Performance Characteristics

| Aspect | Performance |
|--------|-------------|
| Throughput | ~10k-100k ops/sec per leader |
| Latency | ~1-10ms (depends on network) |
| Minimum nodes | 3 (tolerates 1 failure) |
| Maximum practical | 7-9 (more slows elections) |

## Raft in Practice

| System | Implementation |
|--------|---------------|
| etcd | Go, used by Kubernetes |
| Consul | Go, service discovery |
| HashiCorp Nomad | Go, scheduler |
| TiKV | Rust, distributed KV store |
| MongoDB | Raft-based replica sets |
| CockroachDB | Extended Raft for geo-distribution |

**Links**: [[Distributed Systems Design]] | [[System Design Fundamentals]] | [[CAP Theorem and PACELC]] | [[Consistent Hashing]] | [[Database Sharding]]
