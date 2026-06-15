# 08 — Infrastructure & Orchestration

**Links**: [[_MOC]] | [[01 MLOps]] | [[02 Model Serving]] | [[03 Distributed Training]]

AI infrastructure covers compute, orchestration, networking, and storage — everything needed to train and serve models reliably at scale.

## GPU Infrastructure

| GPU | Memory | Interconnect | Best For |
|-----|--------|-------------|----------|
| **NVIDIA A100 (80GB)** | 80 GB HBM2e | NVLink 600 GB/s | Training & serving (sweet spot) |
| **NVIDIA H100 (80GB)** | 80 GB HBM3 | NVLink 900 GB/s | Large model training |
| **NVIDIA H200 (141GB)** | 141 GB HBM3e | NVLink 900 GB/s | Largest models, serving |
| **NVIDIA A10G (24GB)** | 24 GB GDDR6 | PCIe 4.0 | Small model serving, batch inference |
| **NVIDIA L4 (24GB)** | 24 GB GDDR6 | PCIe 4.0 | Cost-effective serving |
| **AMD MI300X (192GB)** | 192 GB HBM3 | Infinity Fabric | High-memory training, competitive pricing |
| **Cloud TPU v5e** | Variable | ICI | Large-scale training (Google Cloud) |

### GPU Memory Hierarchy

```
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
HBM (High Bandwidth Memory)   → 80-192 GB, ~2 TB/s
├── Model weights (quantized) → ~10-20 GB for 70B at INT4
├── KV cache (at 4K context)  → ~2 GB per request at FP16
├── Activations (training)    → Variable, largest consumer
└── Scratch space / temp      → Framework overhead
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
PCIe / NVLink                  → Cross-GPU communication
CPU RAM (optional offload)     → 256-2048 GB, ~50 GB/s
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
```

## Containerization for ML

| Tool | Purpose | Key Feature |
|------|---------|-------------|
| **Docker** | Standard container runtime | NVIDIA Container Toolkit for GPU passthrough |
| **NVIDIA Container Toolkit** | GPU device access in containers | `--gpus all` flag |
| **Enroot** | NVIDIA-optimized container runtime | Faster than Docker for HPC | 
| **Apptainer (Singularity)** | HPC container runtime | No daemon, shared filesystem compatible |

```dockerfile
# Multi-stage build for LLM serving
FROM nvidia/cuda:12.1-runtime AS base
RUN apt-get update && apt-get install -y python3 python3-pip

FROM base AS deps
COPY requirements.txt .
RUN pip install torch --index-url https://download.pytorch.org/whl/cu121
RUN pip install vllm transformers

FROM deps AS final
COPY model/ /model/
COPY server.py /
CMD ["python3", "server.py"]
```

## Kubernetes for ML

| Resource | Purpose | Example |
|----------|---------|---------|
| **Pod** | Basic unit (one or more containers) | One GPU pod per model replica |
| **Deployment** | Manage replicas, rolling updates | Model server with 3 replicas |
| **Service** | Stable network endpoint | Load balancing across model replicas |
| **Ingress** | External access + TLS | API gateway to model endpoints |
| **Horizontal Pod Autoscaler** | Scale replicas on GPU utilization | Scale up when GPU > 80% |
| **Volcano / Kueue** | Batch scheduling for training jobs | Queue GPU jobs, gang scheduling |
| **Node pool** | GPU node pool vs CPU node pool | Separate training and serving |

### Key K8s Config Patterns

```yaml
# GPU pod with resource limits
apiVersion: v1
kind: Pod
spec:
  containers:
  - name: model-server
    image: my-registry/llm-server:latest
    resources:
      limits:
        nvidia.com/gpu: 1  # Request one GPU
      requests:
        nvidia.com/gpu: 1
```

## Ray — Distributed Compute

Ray provides a unified runtime for AI workloads:

| Ray Component | Purpose |
|---------------|---------|
| **Ray Core** | Distributed Python tasks and actors |
| **Ray Train** | Distributed training (Torch, TF, DeepSpeed integration) |
| **Ray Serve** | Model serving with autoscaling, A/B testing |
| **Ray Tune** | Hyperparameter optimization |
| **Ray Data** | Distributed data processing |

```python
# Ray Serve: deploy a model with autoscaling
from ray import serve
from vllm import LLM

@serve.deployment(
    ray_actor_options={"num_gpus": 1},
    autoscaling_config={"min_replicas": 1, "max_replicas": 8}
)
class LLMDeployment:
    def __init__(self):
        self.llm = LLM("meta-llama/Llama-2-7b-chat-hf")

    async def __call__(self, request):
        prompt = request.query_params["prompt"]
        return self.llm.generate(prompt)
```

## Cloud GPU Provisioning

| Provider | GPU Options | Managed ML |
|----------|-------------|------------|
| **AWS** | A100, H100, L4, Inferentia | SageMaker, EKS, ParallelCluster |
| **GCP** | A100, H100, TPU v5e | Vertex AI, GKE, Cloud TPU |
| **Azure** | A100, H100, ND-series | Azure ML, AKS, CycleCloud |
| **Lambda Labs** | A100, H100, H200 | Simple provisioning, competitive pricing |
| **CoreWeave** | A100, H100, L40S | K8s-native, specialized for AI |
| **RunPod** | A100, H100, L40S | Serverless GPU, cheap spot |

## Cost Optimization

| Strategy | Savings | Tradeoff |
|----------|---------|----------|
| **Spot/preemptible instances** | 60-90% | Risk of interruption (use checkpointing) |
| **GPU sharing (MIG/MPS)** | 2-4× utilization | Isolation overhead |
| **Model quantization** | 4× memory | ≤1% accuracy loss |
| **Batching** | 2-10× throughput | Latency increase per request |
| **Caching** | Variable | Cache invalidation complexity |
| **Right-sizing** | 20-40% | Overprovisioned GPU = wasted $ |

**Links**: [[DevOps/Containers/_MOC]] | [[DevOps/Infrastructure/_MOC]] | [[02 Model Serving]] | [[03 Distributed Training]]
