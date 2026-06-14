---
id: a1b2c3d4-1077-4000-8000-000000000077
title: Edge Computing and Edge AI
language: markdown
tags: ''
selection: null
isPinned: false
timestamp: 1781500001077
---
# Edge Computing and Edge AI

Edge computing processes data near its source rather than in a centralized cloud. Edge AI runs ML models on edge devices — phones, cameras, sensors, gateways.

## Cloud vs Edge

| Aspect | Cloud | Edge |
|--------|-------|------|
| Latency | 10-100ms | < 10ms |
| Bandwidth | High cost | Low (only send insights) |
| Privacy | Data leaves device | Data stays local |
| Connectivity | Always online | Works offline |
| Compute | Unlimited | Limited (battery, memory, CPU) |
| Update | Easy | Complex (OTA, rollback) |
| Cost | Pay-as-you-go compute | Higher upfront hardware |

## Edge Tiers

```
Cloud  ──  Central servers, full models, training
  ↑
Gateway ──  Aggregation, filtering, lighter model
  ↑
Device  ──  Sensors, phones, cameras, microcontrollers
```

## Model Optimization for Edge

| Technique | Effect |
|-----------|--------|
| Quantization (FP16 → INT8 → INT4) | 2-4x smaller, 2-3x faster |
| Pruning (remove unimportant weights) | 2-5x smaller |
| Knowledge distillation | Student model mimics teacher |
| Model architecture (MobileNet, EfficientNet-Lite) | Designed for edge |
| ONNX Runtime | Optimized cross-platform inference |
| XNNPACK / CoreML / TFLite | Hardware-optimized backends |

## Edge AI Frameworks

| Framework | Platform | Models |
|-----------|----------|--------|
| TensorFlow Lite | Android, Linux, MCU | TF models |
| Core ML | iOS, macOS | Apple ecosystem |
| ONNX Runtime | Cross-platform | Open standard |
| PyTorch Mobile | Android, iOS | PyTorch models |
| OpenVINO | Intel CPUs, GPUs, VPUs | Intel optimization |
| NCNN | Mobile CPU | Tencent, mobile-optimized |
| ExecuTorch | Mobile, wearable | PyTorch edge runtime |
| MediaPipe | Cross-platform | Vision + text pipeline |

## Edge ML Example

```python
# TensorFlow Lite — deploy on device
import tensorflow as tf

# Convert model
converter = tf.lite.TFLiteConverter.from_saved_model("model")
converter.optimizations = [tf.lite.Optimize.DEFAULT]
converter.target_spec.supported_types = [tf.float16]
tflite_model = converter.convert()

# Save for deployment
with open("model.tflite", "wb") as f:
    f.write(tflite_model)

# On device inference
interpreter = tf.lite.Interpreter(model_path="model.tflite")
interpreter.allocate_tensors()

input_details = interpreter.get_input_details()
output_details = interpreter.get_output_details()

interpreter.set_tensor(input_details[0]["index"], input_data)
interpreter.invoke()
output = interpreter.get_tensor(output_details[0]["index"])
```

## Federated Learning

Train models across edge devices without centralizing data:

```
Server sends global model → Devices train locally → Send gradient updates → Server aggregates
                                                                                    ↓
                                                                          Federated Averaging (FedAvg)
```

## Use Cases

| Domain | Application | Why Edge |
|--------|-------------|----------|
| Autonomous vehicles | Object detection, lane tracking | Latency (milliseconds matter) |
| Smart cameras | Person detection, privacy filtering | Keep video local |
| Wearables | Health monitoring | Offline, privacy, battery |
| Industrial IoT | Predictive maintenance | No cloud connectivity |
| Smart home | Voice assistants | Privacy, offline capability |
| Retail | Inventory tracking | Real-time, low bandwidth |

## Edge Deployment Pipeline

```
Train (GPU) → Optimize (quantize, prune) → Package (TFLite/ONNX) → Deploy (OTA)
                                                                        ↓
                                                            Monitor (accuracy, latency)
                                                                        ↓
                                                            Retrain (if drift detected)
```

## Connectivity Patterns

| Pattern | Description |
|---------|-------------|
| Always online | Stream to cloud for heavy processing |
| Occasional sync | Batch upload when connected |
| Fully offline | All processing on device, sync labels |
| Hybrid | Edge for real-time, cloud for training |

## Challenges

| Challenge | Mitigation |
|-----------|------------|
| Battery consumption | Model optimization, NPU/GPU offload |
| Hardware diversity | ONNX, TFLite universal intermediate format |
| OTA updates | Delta updates, A/B partitions |
| Data drift monitoring | On-device metrics collection |
| Security | Secure enclave, encrypted model storage |

**Links**: [[Serverless Computing]] | [[Cloud Computing]] | [[Model Quantization]] | [[Inference Optimization]] | [[IoT]]
