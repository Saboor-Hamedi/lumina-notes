---
id: a1b2c3d4-1047-4000-8000-000000000047
title: Encoder-Decoder Architecture
language: markdown
tags: ''
selection: null
isPinned: false
timestamp: 1781500001047
---
# Encoder-Decoder Architecture

The encoder-decoder architecture is the original Transformer design. The encoder reads input, and the decoder generates output conditioned on it.

## Architecture Overview

```
Input Tokens → [Encoder Stacks] → Encoder Outputs
                                      ↓
Start Token → [Decoder Stacks] → Output Tokens (one at a time)
                  ↑
         Previous Outputs
```

## Encoder

| Component | Purpose |
|-----------|---------|
| Self-attention | Each token attends to all input tokens |
| Feed-forward | Process each position independently |
| Residual connections | Skip connections for gradient flow |
| Layer norm | Stabilize training |

## Decoder

| Component | Purpose |
|-----------|---------|
| Masked self-attention | Each token attends to earlier positions only |
| Cross-attention | Decoder attends to encoder outputs |
| Feed-forward | Position-wise processing |

## Key Difference: Cross-Attention

The decoder has an additional attention layer (cross-attention) where:

```
Query = Decoder hidden state
Key   = Encoder output
Value = Encoder output
```

This is what makes it a **sequence-to-sequence** model — the decoder can look at the entire input while generating output.

## Training

Both encoder and decoder trained end-to-end:

```python
# Teacher forcing: decoder gets ground truth as input during training
encoder_output = encoder(input_tokens)
decoder_output = decoder(target_tokens[:-1], encoder_output)
loss = cross_entropy(decoder_output, target_tokens[1:])
```

## Inference (Autoregressive)

```
Step 1: [START] → Decoder → "The"
Step 2: [START, The] → Decoder → "cat"
Step 3: [START, The, cat] → Decoder → "sat"
...until [END] token
```

## Prominent Models

| Model | Encoder | Decoder | Parameters | Key Innovation |
|-------|---------|---------|------------|----------------|
| T5 | Encoder | Decoder | 60M-11B | Text-to-text unified format |
| BART | Encoder (Bidirectional) | Decoder (Autoregressive) | 400M | Denoising pretraining |
| Transformer (base) | 6 layers | 6 layers | 65M | Original Vaswani et al. |
| Flan-T5 | Encoder | Decoder | 80M-11B | Instruction-tuned T5 |

## When to Use Encoder-Decoder

| Task | Why Encoder-Decoder Fits |
|------|-------------------------|
| Translation | Full input context + sequential output |
| Summarization | Understand whole document, generate summary |
| Text-to-SQL | Parse question (encoder), generate SQL (decoder) |
| Question answering | Read passage (encoder), generate answer (decoder) |

## vs Decoder-Only (GPT)

| Aspect | Encoder-Decoder | Decoder-Only |
|--------|----------------|--------------|
| Cross-attention | Yes | No |
| Bidirectional input | Yes (encoder) | No (causal) |
| Best for | Seq2seq tasks | Language modeling |
| Scaling | Less tested at scale | Excellent scaling |
| Example | T5, BART | GPT-4, Llama |

**Links**: [[Transformer Architecture]] | [[GPT and Decoder Models]] | [[BERT and Encoder Models]] | [[Self-Attention]] | [[Pre-training and Fine-tuning]]
