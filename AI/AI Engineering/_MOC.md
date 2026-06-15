# AI Engineering — Map of Content

AI Engineering bridges model development and production deployment. It covers the full lifecycle: training at scale, optimization, serving, monitoring, and continuous evaluation.

**Parent**: [[AI/_MOC|AI]]

## Topics

| # | Note | Covers |
|---|------|--------|
| 01 | [[AI/AI Engineering/01 MLOps\|MLOps]] | Pipelines, experiment tracking, model registry, CI/CD, A/B testing, feature stores |
| 02 | [[AI/AI Engineering/02 Model Serving\|Model Serving & Inference]] | vLLM, TGI, Triton, TorchServe, ONNX, TensorRT, batching, caching |
| 03 | [[AI/AI Engineering/03 Distributed Training\|Distributed Training & Scaling]] | DDP, FSDP, DeepSpeed, tensor/pipeline parallelism, data parallelism, mixed precision |
| 04 | [[AI/AI Engineering/04 Model Optimization\|Model Optimization]] | Quantization (GPTQ, AWQ, QAT), pruning, distillation, KV cache optimization, speculative decoding |
| 05 | [[AI/AI Engineering/05 Production LLM\|Production LLM Patterns]] | RAG in prod, prompt management, structured output, context windows, caching strategies |
| 06 | [[AI/AI Engineering/06 Guardrails & Safety\|Guardrails & Safety]] | NeMo Guardrails, content filtering, PII redaction, red-teaming, adversarial robustness |
| 07 | [[AI/AI Engineering/07 Evaluation\|Evaluation & Testing]] | LLM benchmarks (MMLU, HumanEval, HELM), eval frameworks, hallucination detection, A/B testing |
| 08 | [[AI/AI Engineering/08 Infrastructure\|Infrastructure & Orchestration]] | GPU infrastructure, Kubernetes, Ray, Docker for ML, Slurm, cloud GPU provisioning |

## Cross-Domain Links

- [[AI-ML/NLP/LLM/_MOC|LLM]] — Model architectures that AI Engineering deploys
- [[AI-ML/RAG/_MOC|RAG]] — Retrieval patterns for production LLM systems
- [[DevOps/CI-CD/_MOC|CI/CD]] — Pipeline automation
- [[DevOps/Containers/_MOC|Containers]] — Docker/K8s for model serving
- [[DevOps/Monitoring/_MOC|Monitoring]] — Observability for ML systems
- [[System-Design/Databases/_MOC|Databases]] — Vector stores, caching, feature stores

## Learning Path

1. **Start**: Pick your gap — serving (if you have models but can't ship), training (if you can't scale), or eval (if you can't measure)
2. **Build**: Each note is self-contained. Read in any order.
3. **Connect**: Follow cross-links to relate AI Engineering to your existing DevOps and System Design knowledge

## External Resources

- [MLOps.org — Guides & Patterns](https://ml-ops.org/)
- [Made With ML — MLOps Resources](https://madewithml.com/)
- [Chip Huyen's ML Engineering Book](https://github.com/chiphuyen/mlops-book)
- [Full Stack Deep Learning — Course](https://fullstackdeeplearning.com/)
- [Stanford MLSys Seminar](https://mlsys.stanford.edu/)
