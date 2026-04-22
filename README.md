# Self-Pruning Neural Network

## Overview

This project implements a neural network that learns to prune its own weights during training. Instead of applying pruning after training, the model incorporates a learnable gating mechanism that decides which connections are important and which can be removed.

Each weight in the network is associated with a gate value between 0 and 1. When a gate approaches zero, the corresponding weight is effectively removed from the network.

---

## Method

### Prunable Linear Layer

A custom linear layer called `PrunableLinear` is used instead of the standard fully connected layer.

* Each weight has a corresponding learnable gate parameter.
* Gate values are passed through a sigmoid function to keep them between 0 and 1.
* The effective weight is computed as:

  weight × gate

This allows the network to suppress unnecessary connections during training.

---

## Model Architecture

A simple feedforward neural network is used:

* Input: Flattened CIFAR-10 images (32 × 32 × 3)
* Layer 1: PrunableLinear with 512 units
* Layer 2: PrunableLinear with 256 units
* Output Layer: PrunableLinear with 10 units

ReLU activation is applied between layers.

---

## Loss Function

The total loss consists of two components:

Total Loss = Classification Loss + λ × Sparsity Loss

* Classification Loss: CrossEntropyLoss
* Sparsity Loss: Mean of gate values

The sparsity term encourages the network to reduce gate values, effectively removing less important connections.

---

## Why L1 Regularization Encourages Sparsity

The sparsity loss behaves like an L1 penalty on the gate values. L1 regularization encourages values to move toward zero rather than spreading them out.

As a result:

* Many gate values become very small
* Corresponding weights are effectively removed
* The network becomes sparse

---

## Results

| Lambda | Test Accuracy (%) | Sparsity (%) |
| ------ | ----------------- | ------------ |
| 0.001  | 46.82             | 94.01        |
| 0.005  | 47.08             | 95.27        |
| 0.01   | 47.42             | 95.58        |

---

## Observations

* The model achieves very high sparsity while maintaining reasonable accuracy.
* This indicates that a large number of parameters in the network are redundant.
* Even after removing most connections, the model continues to perform well.

---

## Gate Value Distribution

The distribution of gate values shows:

* A strong concentration near zero, indicating pruned connections
* A smaller set of values away from zero, representing important connections

This confirms that the model is effectively learning which weights to keep and which to discard.

Add your plot image here:

![Gate Distribution](gate_distribution.png)

---

## Conclusion

This project demonstrates that neural networks can learn to prune themselves during training using a simple gating mechanism and sparsity regularization.

The approach reduces model complexity while preserving performance, showing the potential for more efficient neural network designs.

---
