# 02 — Model Serving & Inference

**Links**: [[_MOC]] | [[01 MLOps]] | [[04 Model Optimization]] | [[05 Production LLM]]

Serving is the process of running model inference in production — handling requests, batching, caching, and scaling under latency constraints.

## Serving Frameworks

| Framework | Models | Strengths |
|-----------|--------|-----------|
| **vLLM** | LLMs (GPT, LLaMA, Mistral) | PagedAttention, continuous batching, fast |
| **Text Generation Inference (TGI)** | LLMs | HuggingFace-native, safety, watermarking |
| **Triton Inference Server** | Any (TensorRT, ONNX, PyTorch, TF) | Multi-model, multi-framework, concurrent model execution |
| **TorchServe** | PyTorch | Native PyTorch, model versioning, REST/gRPC |
| **ONNX Runtime** | ONNX models | Cross-platform, quantization, graph optimization |
| **Ray Serve** | Any Python | Composable deployments, autoscaling, A/B testing |

## LLM Serving Optimizations

| Technique | What It Does | Latency Impact |
|-----------|-------------|----------------|
| **Continuous batching** | Dynamically add/remove sequences from batch as they finish | 2-5× throughput |
| **PagedAttention** | Manage KV cache in non-contiguous blocks (vLLM) | Near-zero waste, 2-4× throughput |
| **KV cache quantization** | Store cache in FP8/INT4 | ~2× memory savings |
| **Prefix caching** | Cache shared prefix across requests (system prompt) | 40-80% TTFT reduction |
| **Speculative decoding** | Use draft model to propose tokens, verify with target | 1.5-3× throughput |
| **Flash Attention** | Fused attention kernel, tiling | 2-4× speed, linear memory |

```python
# vLLM: OpenAI-compatible API in ~10 lines
from vllm import LLM, SamplingParams

llm = LLM("meta-llama/Llama-2-7b-chat-hf", tensor_parallel_size=2)
params = SamplingParams(temperature=0.7, max_tokens=512)

output = llm.generate("What is MLOps?", params)
print(output[0].outputs[0].text)
```

## Inference Types

| Type | Latency | Throughput | Use Case |
|------|---------|-----------|----------|
| **Online (real-time)** | < 500ms | Low | Chatbots, search, real-time decisions |
| **Streaming** | First token < 200ms | Low | LLM chat, code completion |
| **Batch (offline)** | Hours | High | Bulk scoring, data processing |
| **Edge** | Device-dependent | Low | Mobile, IoT, on-device |

## Batching Strategies

- **Dynamic batching**: Accumulate requests up to a timeout or max batch size
- **Continuous batching**: New sequences join after completed ones leave (LLM-specific)
- **Request multiplexing**: Interleave multiple requests on the same GPU

## GPU Memory Management

| Strategy | Memory Savings | Overhead |
|----------|---------------|----------|
| Model parallelism (tensor) | Split layers across GPUs | Communication |
| Pipeline parallelism | Split layer groups across GPUs | Bubble |
| CPU offloading | Offload idle layers to CPU | 10-100× latency |
| Quantization (FP16→INT4) | 4× | Accuracy impact |

**Links**: [[04 Model Optimization]] | [[05 Production LLM]] | [[08 Infrastructure]] | [[DevOps/Containers/_MOC]]
