---
id: dev-063-0000-0000-0000-000000000052
title: gRPC
language: markdown
tags: ''
selection: null
isPinned: false
timestamp: 1781900000004
---
# gRPC

**Links**: [[REST API Design]] | [[HTTP Protocol]] | [[Data Serialization]] | [[Microservices Architecture]] | [[Service Mesh]] | [[API Gateway Patterns]] | [[Docker Containers]] | [[Kubernetes Basics]] | [[Envoy Proxy]]

**See also**: [[API Security]], [[Docker Compose]], [[Protocol Buffers]]

## What is gRPC?

gRPC is a high-performance RPC framework using HTTP/2 and Protocol Buffers. It enables strongly-typed, streaming API calls between services.

## gRPC vs REST

| Aspect | REST | gRPC |
|--------|------|------|
| Protocol | HTTP/1.1 | HTTP/2 |
| Data format | JSON (text) | Protobuf (binary) |
| Contract | OpenAPI (loose) | .proto (strict) |
| Streaming | No (native) | Bidirectional |
| Browser support | Native | Needs proxy (gRPC-web) |
| Speed | Slower | 5-10x faster |

## Proto Definition

```protobuf
syntax = "proto3";

service UserService {
    rpc GetUser (GetUserRequest) returns (User);
    rpc ListUsers (ListUsersRequest) returns (stream User);
    rpc UpdateUser (stream UpdateUserRequest) returns (User);
}

message User {
    int32 id = 1;
    string name = 2;
    string email = 3;
}

message GetUserRequest {
    int32 id = 1;
}
```

## Server (Python)

```python
import grpc
from concurrent import futures
import user_pb2, user_pb2_grpc

class UserServicer(user_pb2_grpc.UserServiceServicer):
    def GetUser(self, request, context):
        return user_pb2.User(
            id=request.id,
            name="Alice",
            email="alice@example.com"
        )

server = grpc.server(futures.ThreadPoolExecutor(max_workers=10))
user_pb2_grpc.add_UserServiceServicer_to_server(UserServicer(), server)
server.add_insecure_port('[::]:50051')
server.start()
server.wait_for_termination()
```

## Client

```python
import grpc
import user_pb2, user_pb2_grpc

channel = grpc.insecure_channel('localhost:50051')
stub = user_pb2_grpc.UserServiceStub(channel)

response = stub.GetUser(user_pb2.GetUserRequest(id=1))
print(f"User: {response.name}, {response.email}")
```

**Next**: [[Docker Compose]] — Multi-container Docker