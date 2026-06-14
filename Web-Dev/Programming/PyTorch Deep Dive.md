---
id: a1b2c3d4-1201-4000-8000-000000000201
title: PyTorch Deep Dive
language: markdown
tags: [web-dev, programming, pytorch, deep-learning, framework]
selection: null
isPinned: false
timestamp: 1781500001201
---
# PyTorch Deep Dive

PyTorch is an open-source machine learning framework with dynamic computation graphs, GPU acceleration, and a Pythonic API.

## Core Concepts

| Concept | Description |
|---------|-------------|
| Tensor | Multi-dimensional array (like NumPy + GPU) |
| Autograd | Automatic differentiation |
| Module | Neural network layer/component |
| Optimizer | Gradient-based optimization |
| Dataset/DataLoader | Data pipeline |
| Device | CPU/GPU/MPS placement |

## Tensor Basics

```python
import torch
import numpy as np

# Creation
t = torch.tensor([[1, 2], [3, 4]])
t = torch.zeros(3, 4)
t = torch.ones(2, 3)
t = torch.randn(3, 3)          # Standard normal
t = torch.arange(0, 10, 2)     # [0, 2, 4, 6, 8]
t = torch.linspace(0, 1, 5)    # [0, 0.25, 0.5, 0.75, 1]

# From NumPy
t = torch.from_numpy(np.array([1, 2, 3]))

# Properties
t.shape          # torch.Size([3, 4])
t.dtype          # torch.float32
t.device         # cpu / cuda:0
t.requires_grad  # False by default

# Reshaping
t.view(2, 6)             # Reshape (must be contiguous)
t.reshape(2, 6)          # Reshape (handles non-contiguous)
t.transpose(0, 1)        # Swap dimensions
t.permute(2, 0, 1)       # General dimension reorder
t.squeeze()              # Remove size-1 dimensions
t.unsqueeze(0)           # Add dimension at position 0
```

## GPU Acceleration

```python
# Device detection
device = torch.device('cuda' if torch.cuda.is_available() else
                      'mps' if torch.backends.mps.is_available() else 'cpu')

# Move tensors to device
t = torch.randn(3, 3).to(device)
t = torch.randn(3, 3, device=device)

# Move models to device
model = MyModel().to(device)

# Data parallel (multi-GPU)
model = torch.nn.DataParallel(model)

# Mixed precision training (faster, less memory)
scaler = torch.cuda.amp.GradScaler()

with torch.cuda.amp.autocast():
    output = model(x)
    loss = criterion(output, y)

scaler.scale(loss).backward()
scaler.step(optimizer)
scaler.update()
```

## Autograd

```python
# Gradient tracking
x = torch.tensor([1.0, 2.0, 3.0], requires_grad=True)
y = (x ** 2).sum()
y.backward()          # Compute gradients
x.grad                # tensor([2., 4., 6.])

# Detach (remove from computation graph)
x_detached = x.detach()

# Disable gradient (inference)
with torch.no_grad():
    y = model(x)      # No gradient tracking

# Zero gradients
optimizer.zero_grad()
```

## Neural Network Modules

```python
import torch.nn as nn
import torch.nn.functional as F

class SimpleNet(nn.Module):
    def __init__(self, input_size=784, hidden_size=256, num_classes=10):
        super().__init__()
        self.fc1 = nn.Linear(input_size, hidden_size)
        self.fc2 = nn.Linear(hidden_size, hidden_size)
        self.fc3 = nn.Linear(hidden_size, num_classes)
        self.dropout = nn.Dropout(0.3)

    def forward(self, x):
        x = x.view(x.size(0), -1)          # Flatten
        x = F.relu(self.fc1(x))
        x = self.dropout(x)
        x = F.relu(self.fc2(x))
        x = self.dropout(x)
        x = self.fc3(x)
        return x

# Common layers
nn.Conv2d(3, 64, kernel_size=3, padding=1)  # Conv
nn.BatchNorm2d(64)                          # Batch norm
nn.MaxPool2d(2)                             # Max pooling
nn.AdaptiveAvgPool2d(1)                     # Global avg pool
nn.LSTM(512, 256, num_layers=2, batch_first=True)  # LSTM
nn.TransformerEncoderLayer(d_model=512, nhead=8)    # Transformer
nn.Embedding(10000, 300)                    # Embedding
```

## Training Loop

```python
def train_one_epoch(model, dataloader, criterion, optimizer, device):
    model.train()
    total_loss = 0
    correct = 0
    total = 0

    for batch_idx, (inputs, targets) in enumerate(dataloader):
        inputs, targets = inputs.to(device), targets.to(device)

        optimizer.zero_grad()
        outputs = model(inputs)
        loss = criterion(outputs, targets)
        loss.backward()
        optimizer.step()

        total_loss += loss.item()
        _, predicted = outputs.max(1)
        total += targets.size(0)
        correct += predicted.eq(targets).sum().item()

    avg_loss = total_loss / len(dataloader)
    accuracy = 100. * correct / total
    return avg_loss, accuracy


def evaluate(model, dataloader, criterion, device):
    model.eval()
    total_loss = 0
    correct = 0
    total = 0

    with torch.no_grad():
        for inputs, targets in dataloader:
            inputs, targets = inputs.to(device), targets.to(device)
            outputs = model(inputs)
            loss = criterion(outputs, targets)

            total_loss += loss.item()
            _, predicted = outputs.max(1)
            total += targets.size(0)
            correct += predicted.eq(targets).sum().item()

    return total_loss / len(dataloader), 100. * correct / total
```

## Custom Dataset

```python
from torch.utils.data import Dataset, DataLoader

class CustomDataset(Dataset):
    def __init__(self, data_path, transform=None):
        self.data = pd.read_parquet(data_path)
        self.transform = transform

    def __len__(self):
        return len(self.data)

    def __getitem__(self, idx):
        row = self.data.iloc[idx]
        image = row['image']        # Assuming pre-loaded
        label = row['label']

        if self.transform:
            image = self.transform(image)

        return image, label

# DataLoader
dataset = CustomDataset('data.parquet', transform=transforms)
dataloader = DataLoader(
    dataset,
    batch_size=32,
    shuffle=True,
    num_workers=4,
    pin_memory=True,
    drop_last=True,
)
```

## Saving and Loading

```python
# Save checkpoint
torch.save({
    'epoch': epoch,
    'model_state_dict': model.state_dict(),
    'optimizer_state_dict': optimizer.state_dict(),
    'loss': loss,
}, 'checkpoint.pth')

# Load checkpoint
checkpoint = torch.load('checkpoint.pth')
model.load_state_dict(checkpoint['model_state_dict'])
optimizer.load_state_dict(checkpoint['optimizer_state_dict'])

# Save for inference
torch.onnx.export(model, dummy_input, 'model.onnx')

# TorchScript (JIT)
traced_model = torch.jit.trace(model, example_input)
traced_model.save('model.pt')
```

**Links**: [[Async Python]] | [[C and C++]] | [[C Sharp and DotNET]] | [[Compiler Design]] | [[Dart and Flutter]] | [[Elixir and Erlang]] | [[Finite Automata and Formal Languages]] | [[Flutter Deep Dive]] | [[Functional Programming Concepts]] | [[Functional Programming]] | [[Go Concurrency Patterns]] | [[Go Programming]] | [[Haskell]] | [[Java]] | [[Julia]] | [[Kotlin]] | [[Lua Scripting]] | [[Object-Oriented Programming]] | [[Pandas for Data Analysis]] | [[PHP]] | [[Programming Language Paradigms]] | [[Python Deep Dive]] | [[Python Imports and Modules]] | [[Python Type Hints]] | [[Python Virtual Environments]] | [[R for Data Science]] | [[Ruby]] | [[Rust Ownership and Borrowing]] | [[Rust]] | [[Scala]] | [[scikit-learn Deep Dive]] | [[Swift and iOS Development]] | [[TypeScript]]
