---
id: a1b2c3d4-1033-4000-8000-000000000033
title: Computer Vision
language: markdown
tags: [ai-ml, deep-learning, computer-vision]
selection: null
isPinned: false
timestamp: 1781500001033
---
---
id: a1b2c3d4-1033-4000-8000-000000000033
title: Computer Vision
language: markdown
tags: ''
selection: null
isPinned: false
timestamp: 1781500001033
---

# Computer Vision

**Links**: [[Diffusion Models]] | [[CNNs for NLP]] | [[Transformer Architecture]] | [[Multi-Modal RAG]] | [[Computer Architecture and Organization]]

**See also**: [[Scalable Vector Graphics]], [[WebAssembly]] (in-browser CV), [[Edge Computing and Edge AI]]

Computer Vision (CV) enables machines to interpret and understand visual information from images and video.

## Core Tasks

| Task | Description | Example |
|------|-------------|---------|
| Image Classification | Assign a label to an image | "cat" vs "dog" |
| Object Detection | Locate + classify objects | Bounding boxes |
| Semantic Segmentation | Classify each pixel | Road, car, pedestrian |
| Instance Segmentation | Detect + segment each object instance | Each car gets its own mask |
| Keypoint Detection | Identify specific points | Human pose estimation |
| Image Generation | Create new images | Stable Diffusion, DALL-E |
| Visual Q&A | Answer questions about images | "What color is the car?" |

## Convolutional Neural Networks (CNNs)

```
Input → Conv → ReLU → Pool → Conv → ReLU → Pool → FC → Output
```

| Layer | Purpose |
|-------|---------|
| Convolution | Detect features (edges, textures, patterns) |
| ReLU | Non-linearity |
| Pooling | Downsample (max/avg) |
| Fully Connected | Classification head |

## Key Architectures

| Model | Year | Innovation |
|-------|------|------------|
| LeNet-5 | 1998 | First CNN for digit recognition |
| AlexNet | 2012 | Deep CNN, ReLU, GPU training |
| VGGNet | 2014 | Very deep (16-19 layers), simple design |
| ResNet | 2015 | Skip connections (solved vanishing gradient) |
| EfficientNet | 2019 | Neural architecture search for optimal scaling |
| Vision Transformer (ViT) | 2020 | Transformer applied to image patches |
| YOLO | 2016+ | Real-time object detection |

## Image Augmentation

```python
transforms = [
    RandomHorizontalFlip(),
    RandomRotation(20),
    ColorJitter(brightness=0.2, contrast=0.2),
    RandomResizedCrop(224),
    Normalize(mean=[0.485, 0.456, 0.406], std=[0.229, 0.224, 0.225])
]
```

## Transfer Learning

Use pre-trained models (ImageNet weights) and fine-tune:

1. Remove classification head
2. Replace with new head (your classes)
3. Freeze base layers, train head
4. Optionally fine-tune base with lower learning rate

## Common Frameworks

| Framework | Best For |
|-----------|----------|
| PyTorch | Research, flexibility |
| TensorFlow/Keras | Production, deployment |
| OpenCV | Real-time processing, traditional CV |
| Detectron2 | Object detection and segmentation |
| MMDetection | Modular detection framework |

## Evaluation Metrics

| Metric | Task | Meaning |
|--------|------|---------|
| Accuracy | Classification | Correct / total |
| Precision | Detection | True positives / (TP + FP) |
| Recall | Detection | True positives / (TP + FN) |
| mAP (mean Average Precision) | Detection | Average precision across IoU thresholds |
| IoU (Intersection over Union) | Detection | Overlap between prediction and ground truth |

**Links**: [[CNNs for NLP]] | [[Transformer Architecture]] | [[Data Augmentation for NLP]] | [[NLP Pipeline Design]] | [[Model Quantization]]
