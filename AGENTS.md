# UniSem Agent Guide

## Project Overview

UniSem (Universal Semantic Representations) investigates whether a compact
semantic representation can serve as reusable infrastructure across
heterogeneous edge applications, vendors, domains, deployment environments,
and downstream tasks.

The central vision is:

> A universal semantic representation can act as a reusable substrate for edge
> intelligence, reducing duplicated models and integration cost while
> preserving application utility.

The primary goal is **representation reuse**, not multi-task optimization.
Multiple downstream tasks are evidence used to test semantic reuse; they are
not the project's defining architecture or objective.

## Project Scope

UniSem is **not** primarily about:

- A particular encoder family
- Jointly optimizing a fixed collection of tasks
- Model compression as the primary research contribution
- Image generation
- Diffusion models
- Large language models
- Achieving state-of-the-art performance on one benchmark

UniSem **is** about:

- Reusable semantic representations
- Semantic infrastructure for edge intelligence
- Representation transfer and reuse
- Cross-application and cross-domain scalability
- Cross-vendor interoperability
- Compression that preserves broad semantic utility
- Efficient and maintainable edge deployment

## Primary Research Question

Can a universal semantic representation act as reusable infrastructure for
heterogeneous edge intelligence?

## Secondary Research Questions

1. How well does a representation transfer across tasks with different
   semantic and spatial requirements?
2. Can a representation be reused by independently developed applications and
   stakeholders?
3. How robust is reuse across domains, sensors, and deployment environments?
4. Can foundation representations be compressed without destroying their
   reusable utility?
5. What storage, memory, latency, energy, and maintenance gains result from
   semantic reuse?
6. Where is specialization still required, and what determines that boundary?

## Reuse Taxonomy

### Task Reuse

One representation supports different downstream tasks. Classification,
detection, segmentation, retrieval, and anomaly detection are initial evidence
workloads.

### Application Reuse

Multiple applications consume a common representation while retaining
independent downstream logic, release cycles, and quality requirements.

### Vendor Reuse

Different stakeholders share a representation contract or semantic interface
without requiring identical downstream consumers, runtimes, or application
stacks.

### Domain Reuse

The representation transfers across datasets, sensors, environments,
geographies, and operating conditions.

## Design Principles

### 1. Representation First

Evaluate semantic quality, transferability, and reuse before increasing model
or system complexity.

### 2. Reuse Before Joint Optimization

Prefer experiments where independently trained downstream components consume a
fixed representation. Joint optimization may be studied as a comparison, but
it does not establish reusable infrastructure by itself.

### 3. Simplicity Before Novelty

Establish strong, reproducible baselines before proposing new methods.

### 4. Compression After Validation

Do not optimize representation size until reusable utility has been
demonstrated across tasks and domains.

### 5. Stable Semantic Interface

Prefer:

```text
Inputs
  -> Reusable Semantic Substrate
  -> Independent Applications
```

over:

```text
Application
  -> Dedicated End-to-End Model
```

Study representation shape, versioning, compatibility, and feature semantics
as infrastructure-level concerns.

### 6. Edge-Centric Evaluation

Every candidate should eventually be evaluated on:

- Model and feature footprint
- Memory usage
- FLOPs
- Latency and throughput
- Energy consumption
- Application onboarding and update cost
- Portability across devices, runtimes, and vendors

## Candidate Representation Families

Initial candidates include:

1. DINOv2
2. SigLIP2
3. CLIP
4. RAE-based representations
5. JEPA-based representations
6. Future foundation representations

These are candidate implementations, not the scientific contribution. Do not
assume that any listed family is the final solution. Use the same evaluation
protocol to compare new candidates.

## Experimental Priorities

### Experiment 1: Representation Reusability

Evaluate DINOv2, SigLIP2, and CLIP on classification, detection, segmentation,
retrieval, and anomaly detection. Measure task performance, linear probing,
few-shot adaptation, and Representation Reuse Ratio (RRR).

### Experiment 2: Compression-Aware Semantic Preservation

Evaluate the pipeline:

```text
Foundation Encoder
  -> Distillation
  -> Structured Pruning
  -> Quantization
```

Measure utility retention, representation drift, performance retention, and
Semantic Utility Retention (SUR). Compression is a preservation test, not the
primary project contribution.

### Experiment 3: Cross-Application Reuse

Use one frozen encoder for traffic monitoring, industrial inspection, retail
analytics, and security surveillance. Only lightweight heads may change.
Measure transferability, adaptation cost, performance retention, and
Cross-Application Reuse Score (CARS).

### Experiment 4: Edge-System Evaluation

Compare one dedicated model per application with one shared encoder plus
lightweight heads. Measure memory, storage, latency, energy, deployment
complexity, and Utility-Efficiency Score (UES).

## Repository-Level Exploratory Metrics

- **Representation Reuse Ratio (RRR):** fraction of candidate applications or
  workloads that can reuse one representation while meeting predefined utility
  thresholds.
- **Semantic Utility Retention (SUR):** normalized reusable utility preserved
  after distillation, structured pruning, and quantization.
- **Cross-Application Reuse Score (CARS):** balanced measure of application
  utility retention and adaptation efficiency under one frozen encoder.
- **Utility-Efficiency Score (UES):** joint, workload-specific measure of
  reusable utility and measured resource savings.

These are exploratory research metrics. Always report their definitions,
normalization, uncertainty, sensitivity, and underlying raw measurements.

## Coding and Research Philosophy

All contributions should be:

- Modular
- Reproducible
- Research-oriented
- Minimal in dependencies
- Configuration-driven
- Explicit about assumptions and controls

Prefer frozen-representation baselines, independent application adaptation,
held-out tasks and domains, deterministic evaluation where practical, and
results that can be reproduced independently.

## Success Criteria

A successful UniSem result should show that a semantic representation:

- Is reusable across heterogeneous edge workloads
- Transfers across applications or domains with limited adaptation
- Supports independent downstream development
- Preserves useful semantics after meaningful compression
- Reduces measured deployment or operational cost
- Exposes clear limits where specialization remains necessary

Always prioritize scientific validity over architectural novelty or engineering
complexity.
