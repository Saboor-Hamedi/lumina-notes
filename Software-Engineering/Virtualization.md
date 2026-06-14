---
id: a1b2c3d4-1039-4000-8000-000000000039
title: Virtualization
language: markdown
tags: [software-engineering]
selection: null
isPinned: false
timestamp: 1781500001039
---
# Virtualization

Virtualization abstracts physical hardware, enabling multiple virtual machines (VMs) or containers to run on shared hardware with isolation.

## Hypervisors

| Type | How It Works | Example |
|------|-------------|---------|
| Type 1 (Bare Metal) | Runs directly on hardware | VMware ESXi, Hyper-V, KVM |
| Type 2 (Hosted) | Runs on top of host OS | VirtualBox, VMware Workstation |

```
Type 1:   ┌─────┬─────┬─────┐
          │ VM  │ VM  │ VM  │
          │     │     │     │
          ├─────┴─────┴─────┤
          │   Hypervisor    │
          ├─────────────────┤
          │    Hardware     │
          └─────────────────┘

Type 2:   ┌─────┬─────┬─────┐
          │ VM  │ VM  │ VM  │
          ├─────┴─────┴─────┤
          │   Host OS       │
          ├─────────────────┤
          │   Hypervisor    │
          ├─────────────────┤
          │    Hardware     │
          └─────────────────┘
```

## Virtual Machines vs Containers

| Aspect | VM | Container |
|--------|----|-----------|
| Guest OS | Full OS per VM | Shares host kernel |
| Isolation | Strong (hardware-level) | Process-level |
| Boot time | Minutes | Seconds |
| Image size | GBs | MBs |
| Resource overhead | Higher (duplicate OS) | Lower (shared kernel) |
| Density | 10s per host | 100s-1000s per host |

## Paravirtualization vs Full Virtualization

| Approach | How Guest Knows | Performance |
|----------|----------------|-------------|
| Full Virtualization | Guest is unaware | Slightly slower (binary translation) |
| Paravirtualization | Guest modified to call hypervisor | Near-native |
| Hardware-Assisted | CPU has virtualization extensions (VT-x, AMD-V) | Near-native |

## Paravirtualization

Guest OS is modified to replace privileged instructions with hypercalls — direct calls to the hypervisor. Improves performance vs binary translation.

## Hardware-Assisted Virtualization

Modern CPUs include virtualization extensions:
- **Intel VT-x** / **AMD-V**: CPU-level support for VM operations
- **SR-IOV**: PCIe devices can be directly assigned to VMs
- **IOMMU**: Hardware memory mapping for device passthrough

## Storage Virtualization

| Type | Description |
|------|-------------|
| Thick Provisioning | Pre-allocate full size |
| Thin Provisioning | Allocate on demand |
| Snapshots | Point-in-time copy (COW) |
| Live Migration | Move VM without downtime |

## Networking Virtualization

| Component | Purpose |
|-----------|---------|
| vSwitch | Virtual switch for VM traffic |
| VLAN | Network segmentation |
| VXLAN | Overlay network (Layer 2 over Layer 3) |
| NFV | Network functions as VMs (firewall, load balancer) |

## Use Cases

- **Server consolidation**: Replace many physical servers with VMs on fewer hosts
- **Cloud computing**: Multi-tenant infrastructure foundation
- **Development/testing**: Isolated environments per dev
- **Disaster recovery**: Snapshot and replicate VMs
- **Legacy support**: Run old OS on modern hardware

**Links**: [[Docker Containers]] | [[Kubernetes Basics]] | [[Operating Systems]] | [[Cloud Computing]] | [[Serverless Computing]]
