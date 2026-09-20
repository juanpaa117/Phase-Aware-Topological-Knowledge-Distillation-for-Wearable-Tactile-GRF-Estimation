# Phase-Aware Topological Knowledge Distillation for Wearable Tactile GRF Estimation

This repository provides the training and evaluation code for the student model used in our knowledge distillation framework for ground reaction force (GRF) estimation from bilateral plantar pressure sequences.

The released code focuses on student distillation and includes the components required to reproduce the proposed knowledge distillation procedure.

## Overview

The framework transfers spatiotemporal knowledge from a pretrained teacher network to a lightweight student model.

During training, the student receives supervision from:

* GRF regression targets;
* teacher–student temporal relations;
* topological representations based on persistent homology;
* phase-aware supervision.

The distillation-specific modules are used only during training. At inference time, only the lightweight student network is required.

## Repository Structure

```text
.
├── train_student.py
├── evaluate.py
├── run_train.sh
├── run_eval.sh
├── requirements.txt
│
├── models/
│   ├── teacher.py
│   └── student.py
│
├── distillation/
│   ├── temporal_relation.py
│   ├── topology.py
│   └── phase_head.py
│
├── utils/
│   ├── dataset.py
│   └── metrics.py
│
└── checkpoints/
    ├── teacher_T1_W200_fold_S01.pth
    └── student_T1_W200_fold_S01.pth
```

## Environment

The experiments were implemented in Python using PyTorch.

Install the required packages with:

```bash
pip install -r requirements.txt
```

## Data

The input consists of bilateral plantar-pressure sequences paired with bilateral GRF signals.

The experiments follow a leave-one-subject-out (LOSO) evaluation protocol. For each fold, one subject is held out for testing and the remaining subjects are used for training.

The raw dataset is not included in this repository.

The expected dataset directory should follow the structure described below:

```text
DATA_ROOT/
├── ...
```

## Training

A pretrained teacher model is used during student distillation and remains frozen throughout training.

An example training configuration can be executed with:

```bash
bash run_train.sh
```

The provided example corresponds to:

```text
Teacher       : T1
Window length : 200
Protocol      : LOSO
Held-out fold : S01
Epochs        : 200
Batch size    : 128
```

The exact hyperparameters used in the paper are defined in the training script and shell configuration.

## Evaluation

The pretrained student checkpoint can be evaluated with:

```bash
bash run_eval.sh
```

The evaluation reports:

* RMSE
* MAE
* Pearson correlation coefficient

For configurations using phase supervision, phase accuracy and macro F1 score can also be reported.

## Pretrained Models

For demonstration and reproducibility, we provide pretrained teacher and student checkpoints from one representative LOSO fold:

```text
checkpoints/
├── teacher_T1_W200_fold_S01.pth
└── student_T1_W200_fold_S01.pth
```

The `S01` designation indicates that Subject 01 is the held-out test subject for this LOSO fold.

The teacher checkpoint is provided so that the student distillation procedure can be executed without retraining the teacher model.

## Training and Inference

During training:

```text
Pretrained Teacher
        │
        ├── Temporal Relation Distillation
        ├── Topological Distillation
        └── Phase-Aware Supervision
                    │
                    ▼
             Student Network
```

During inference:

```text
Plantar Pressure → Student Network → GRF
```

The teacher and auxiliary distillation modules are not required during inference.

## Notes

This repository contains the code required for the student knowledge distillation experiments. Teacher training, unrelated baselines, visualization scripts, and additional experimental utilities are omitted from this anonymous release.

Additional code and pretrained models may be released after completion of the review process.
