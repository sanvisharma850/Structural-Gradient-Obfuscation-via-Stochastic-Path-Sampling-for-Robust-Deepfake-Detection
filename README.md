
# Structural Gradient Obfuscation (SGO) for Robust Deepfake Detection

## Overview

This project proposes a new approach to defending deepfake detection models against **adaptive adversarial attacks**. Instead of relying on a single fixed neural network, the model is designed as a **stochastic super-network** that randomly selects different architectural paths during inference.

The key idea is that if different paths produce significantly different gradients, then an attacker cannot reliably optimize an adversarial input using gradient-based methods. This effect is referred to as **Structural Gradient Obfuscation (SGO)**.

The project focuses on studying the relationship between:

* architectural randomness,
* gradient diversity,
* and resistance to adaptive adversarial attacks.

---

## Motivation

Modern deepfake detectors achieve high accuracy on benchmark datasets, but they remain vulnerable to adaptive attacks where the attacker:

* has full knowledge of the model,
* computes gradients directly,
* iteratively modifies a fake video until it bypasses the detector.

Traditional defenses try to improve robustness by:

* adversarial training,
* stronger feature extraction,
* or improved architectures.

This project explores a different direction:

> Instead of making a single model stronger, make the model **structurally unpredictable**.

---

## Core Idea

The system is based on three main components:

### 1. Super-Network Architecture

A weight-sharing super-network is trained instead of a single deepfake detector.
Each layer contains multiple candidate operations, such as:

* spatial feature blocks,
* frequency-domain feature blocks,
* temporal consistency modules,
* transformer attention blocks.

During training, different paths through the network are randomly activated.

---

### 2. Stochastic Path Sampling (SPS)

During inference, a random architectural path is selected for each input.

This means:

* the internal computation graph changes every time,
* the gradient of the loss with respect to the input also changes,
* gradient-based adversarial attacks become harder to optimize.

---

### 3. Inter-Path Gradient Dissimilarity (IPGD)

The project introduces a new metric:

**IPGD (Inter-Path Gradient Dissimilarity)**

This measures how different the gradients are when the same input is processed using different architectural paths.

The main hypothesis of the project is:

> Higher gradient dissimilarity leads to stronger resistance against adaptive adversarial attacks.

---

## Project Goals

The project aims to:

* build a stochastic deepfake detection architecture,
* measure gradient diversity across architectural paths,
* evaluate robustness against adaptive adversarial attacks,
* analyze the relationship between gradient diversity and attack success rate,
* introduce a new metric for evaluating adversarial robustness in deepfake detection.

---

## Repository Structure

```
SGO-Deepfake-Defense/
│
├── models/
│   ├── supernet.py
│   ├── stochastic_blocks.py
│   ├── transformer_blocks.py
│
├── training/
│   ├── train_supernet.py
│   ├── train_baseline.py
│   ├── scheduler.py
│
├── attacks/
│   ├── fgsm.py
│   ├── pgd.py
│   ├── eot_attack.py
│
├── evaluation/
│   ├── gradient_dissimilarity.py
│   ├── robustness_tests.py
│   ├── variance_analysis.py
│
├── datasets/
│   ├── faceforensics_loader.py
│   ├── celebdf_loader.py
│
├── configs/
│   ├── training_config.yaml
│   ├── model_config.yaml
│
├── utils/
│   ├── metrics.py
│   ├── logging.py
│
└── README.md
```

---

## Methodology

### Step 1: Train a Baseline Model

A standard deepfake detection model is trained first.
This provides:

* a performance baseline,
* a comparison point for robustness evaluation.

---

### Step 2: Train the Super-Network

The baseline model is extended into a super-network by introducing multiple candidate operations at each layer.

During training:

* one path is randomly selected at each iteration,
* weights are shared across paths,
* multiple sub-networks become functional.

---

### Step 3: Stochastic Inference

During testing:

* the model randomly samples different architectural paths,
* predictions are generated using these paths,
* results may be averaged for high-security scenarios.

---

### Step 4: Gradient Diversity Analysis

For the same input:

* gradients are computed using multiple paths,
* cosine similarity between gradients is measured,
* gradient dissimilarity is analyzed.

---

### Step 5: Adversarial Robustness Testing

The model is evaluated against:

* FGSM attacks,
* PGD attacks,
* Expectation over Transformation (EoT) attacks,
* adaptive white-box adversarial attacks.

---

## Dataset

The project uses publicly available deepfake datasets:

### FaceForensics++

Used as the primary training dataset because it:

* contains multiple manipulation methods,
* includes both high-quality and compressed videos,
* is widely used in deepfake detection research.

---

### Celeb-DF v2

Used for testing generalization because it:

* contains more realistic deepfakes,
* has fewer visible artifacts,
* is more challenging than FaceForensics++.

---

## Requirements

Install dependencies using:

```
pip install -r requirements.txt
```

Main dependencies include:

* Python 3.9+
* PyTorch
* torchvision
* numpy
* opencv-python
* matplotlib
* scikit-learn

---

## How to Train

### Train baseline model

```
python training/train_baseline.py
```

---

### Train super-network

```
python training/train_supernet.py
```

---

## How to Evaluate Gradient Dissimilarity

```
python evaluation/gradient_dissimilarity.py
```

This script:

* samples multiple architectural paths,
* computes gradients for each path,
* measures cosine similarity,
* reports the IPGD score.

---

## How to Test Adversarial Robustness

```
python evaluation/robustness_tests.py
```

This script evaluates:

* clean accuracy
* adversarial accuracy
* number of attack iterations required for a successful attack
* robustness under adaptive attacks

---

## Expected Results

If the hypothesis is correct, the project should show:

* higher gradient diversity in the super-network compared to the baseline model
* slower convergence of adaptive adversarial attacks
* improved robustness under white-box attack conditions
* minimal loss in clean detection accuracy

---

## Research Contribution

This project introduces:

* a stochastic architecture approach for deepfake detection,
* a new gradient-based robustness metric (IPGD),
* experimental analysis of gradient diversity and adversarial robustness,
* a new direction for defending deepfake detection systems against adaptive attacks.

---

## Future Work

Possible extensions include:

* applying the method to multimodal deepfake detection,
* improving path diversity using better regularization methods,
* testing on larger datasets,
* exploring theoretical connections between gradient variance and adversarial robustness.

---

## License

This project is released for research and academic use
