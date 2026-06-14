---
id: new-007-0000-0000-0000-000000000007
title: Data Serialization
language: markdown
tags: ''
selection: null
isPinned: false
timestamp: 1781600000007
---
# Data Serialization

**Links**: [[REST API Design]] | [[GraphQL API Design]] | [[HTTP Protocol]] | [[API Documentation]] | [[Message Queues]]

## What is Serialization?

Serialization converts in-memory data structures into a format that can be stored or transmitted and reconstructed later.

## Common Formats

| Format | Type | Readability | Schema | Binary | Size |
|--------|------|-------------|--------|--------|------|
| **JSON** | Text | Human | Optional | No | Medium |
| **XML** | Text | Human | Yes (XSD) | No | Large |
| **YAML** | Text | Very human | No | No | Medium |
| **Protobuf** | Binary | Machine | Required | Yes | Small |
| **MessagePack** | Binary | Machine | No | Yes | Small |
| **Avro** | Binary | Machine | Required | Yes | Small |
| **CBOR** | Binary | Machine | No | Yes | Small |

## JSON

```json
{
  "name": "Alice",
  "age": 30,
  "roles": ["admin", "user"],
  "address": {
    "city": "New York",
    "zip": "10001"
  }
}
```

## Protocol Buffers

```protobuf
// user.proto
syntax = "proto3";

message User {
  string name = 1;
  int32 age = 2;
  repeated string roles = 3;
  Address address = 4;
}

message Address {
  string city = 1;
  string zip = 2;
}
```

```python
import user_pb2

user = user_pb2.User(name="Alice", age=30)
data = user.SerializeToString()     # Binary: ~15 bytes
parsed = user_pb2.User()
parsed.ParseFromString(data)
```

## Choosing a Format

| Need | Choose |
|------|--------|
| Human readability, web APIs | JSON |
| Configuration files | YAML |
| High-performance, cross-language | Protobuf |
| Big data / streaming (schema evolution) | Avro |
| Legacy enterprise systems | XML |

**Next**: [[Agile Development]] — Scrum, Kanban, and more