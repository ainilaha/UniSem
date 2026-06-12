# Experimental Plan for Universal Semantic Representations for Reusable Edge Intelligence

## Document Purpose

This document is the master experimental blueprint for UniSem. It tracks the
planned experiments, candidate representations, datasets, controls, metrics,
ablations, expected outcomes, and decision criteria required to evaluate
reusable semantic infrastructure for edge intelligence.

The central hypothesis is:

> A compact semantic representation can be reused across heterogeneous edge
> workloads and applications while preserving useful information and reducing
> deployment cost relative to dedicated application models.

UniSem is not primarily a multi-task learning study and is not primarily a
model compression study. Task diversity is used to probe representation reuse.
Compression is used to test whether reusable semantic utility survives
deployment optimization.

All thresholds in this document are provisional preregistration targets. They
must be frozen before final model comparison and revised only through a
documented amendment based on pilot feasibility, not final test performance.

## 1. Experimental Objectives

### 1.1 Primary Goal

Determine whether a compact semantic representation can be reused across
heterogeneous edge-intelligence workloads while maintaining utility and
reducing deployment cost.

The primary comparison is between:

```text
Traditional:
Application -> Dedicated Encoder -> Application Output
```

and:

```text
UniSem:
Input -> Reusable Semantic Encoder -> Lightweight Application Head
```

Evidence must show more than broad benchmark performance. A reusable
representation should support independently trained consumers, remain useful
under domain and application changes, expose a stable feature contract, and
produce measurable system benefit.

### 1.2 Secondary Goals

#### Representation Transferability

Measure how well frozen representations support tasks not used to specialize
the encoder, under linear-probe, lightweight-head, and few-shot adaptation
regimes.

#### Compression Robustness

Determine whether distillation, structured pruning, and quantization preserve
the semantic information required by diverse workloads.

#### Cross-Domain Reuse

Measure utility under changes in dataset, sensor, environment, geography,
appearance, and application context.

#### Deployment Efficiency

Measure whether replacing dedicated encoders with one reusable encoder reduces
memory, storage, latency, energy, and deployment complexity on representative
edge systems.

### 1.3 General Experimental Rules

- Freeze dataset splits, primary metrics, adaptation budgets, and thresholds
  before final evaluation.
- Use at least three random seeds for trained heads and adaptation studies
  unless compute constraints are documented.
- Report confidence intervals and effect sizes, not only point estimates.
- Keep encoder weights frozen in primary reuse experiments.
- Treat fine-tuning as a diagnostic baseline, not evidence of direct reuse.
- Match head capacity, optimization budget, and input resolution where
  scientifically appropriate.
- Report per-workload results before exploratory aggregate scores.
- Separate representation benefits from model-scale, pretraining-data, and
  compression effects through controlled ablations.
- Audit dataset licenses, duplicates, and likely foundation-model
  pretraining contamination before final claims.
- Preserve negative results and failed operating points in the experiment
  registry.

## 2. Experimental Structure

| Group | Name | Scientific role | Primary evidence |
|---|---|---|---|
| A | Representation Reusability | Tests whether one frozen representation contains useful information for workloads with different semantic and spatial requirements | Task utility, linear probes, few-shot curves, RRR |
| B | Compression-Aware Semantic Preservation | Tests whether reusable information survives deployment-oriented compression | Utility retention, representation drift, compression ratio, SUR |
| C | Cross-Application Reuse | Tests whether independently developed edge applications can share the same encoder and representation contract | Transferability, adaptation cost, retention, CARS |
| D | Edge-System Evaluation | Tests whether representation reuse produces real system benefit relative to dedicated application models | Memory, storage, latency, energy, complexity, UES |

### Group A Rationale

Classification, detection, segmentation, retrieval, and anomaly detection
require different combinations of global semantics, local geometry, dense
features, metric structure, and sensitivity to rare deviations. Success across
these workloads provides evidence that the representation is reusable rather
than narrowly specialized.

### Group B Rationale

An uncompressed representation may be reusable but impractical at the edge.
Compression is therefore evaluated as a semantic preservation problem. The
scientific question is not simply how small the encoder becomes, but which
forms of reusable information are lost at each compression stage.

### Group C Rationale

Task breadth alone does not demonstrate infrastructure reuse. Traffic,
industrial, retail, and security applications introduce different data
distributions, operational objectives, and adaptation costs. Requiring one
frozen encoder across all four applications tests reuse at the application
boundary.

### Group D Rationale

Parameter count and FLOPs do not establish deployment efficiency. The final
comparison must include feature computation, heads, memory traffic, caching,
runtime behavior, energy, and operational complexity under realistic
application portfolios.

## 3. Representation Reusability Experiments

### 3.1 Research Question

Can a single semantic representation support multiple edge workloads?

### 3.2 Candidate Encoders

- DINOv2
- SigLIP2
- CLIP

The initial benchmark is deliberately limited to these three reproducible
candidate families. RAE-, JEPA-, and future foundation representations may be
added later under the same protocol. No candidate encoder is treated as the
UniSem contribution.

### 3.3 Shared Protocol

For each encoder:

1. Freeze all encoder parameters.
2. Extract a documented layer or feature hierarchy at a fixed input
   resolution.
3. Train task-appropriate linear probes where meaningful.
4. Train capacity-matched lightweight heads.
5. Evaluate few-shot adaptation using fixed label budgets.
6. Compare with a dedicated task-specific reference and a conventional
   pretrained baseline.
7. Record feature dimension, extraction cost, and head cost separately.

Proposed few-shot budgets are 1%, 5%, 10%, and 25% of the training labels,
with class-balanced sampling where applicable. Retrieval and anomaly-detection
budgets should be adapted to their supervision structure and documented
before execution.

### 3.4 Planned Workload Matrix

| Experiment ID | Workload | Objective | Proposed dataset | Primary metrics | Expected outcome | Minimum success criterion |
|---|---|---|---|---|---|---|
| A-CLS | Classification | Test global semantic separability and data-efficient adaptation | ImageNet-1K; CIFAR-100 as pilot | Top-1 accuracy, linear-probe accuracy, few-shot accuracy, calibration | Foundation features support competitive frozen classification | At least one encoder retains at least 90% of dedicated-reference normalized utility |
| A-DET | Object detection | Test preservation of object semantics, localization, and scale | COCO 2017 | Box mAP, AP50, AP75, small/medium/large-object AP | Intermediate or multi-scale features support localization without encoder tuning | At least one encoder retains at least 85% of dedicated-reference normalized utility |
| A-SEG | Semantic segmentation | Test dense spatial and class information | ADE20K; Cityscapes for domain transfer | mIoU, mean accuracy, boundary IoU | Spatially resolved foundation features remain useful for dense prediction | At least one encoder retains at least 85% of dedicated-reference normalized utility |
| A-RET | Image retrieval | Test representation geometry and reusable similarity | Revisited Oxford/Paris; optionally Flickr30k for cross-modal retrieval | mAP, Recall@1/5/10, neighborhood consistency | Shared embeddings provide useful instance or semantic ranking | At least one encoder retains at least 90% of dedicated-reference normalized utility |
| A-ANO | Anomaly detection | Test sensitivity to rare, local, and distribution-specific deviations | MVTec AD; VisA as external validation | Image AUROC, pixel AUROC, AUPRO, F1 | Frozen features expose anomalies despite no anomaly-oriented pretraining | At least one encoder retains at least 85% of dedicated-reference normalized utility |

Dataset substitutions are permitted only when access, licensing, contamination,
or compute constraints are recorded. Substitutes must preserve the scientific
capability tested by the original workload.

### 3.5 Measurements

#### Task Utility

- Accuracy and calibration for classification
- Box mAP for detection
- mIoU and boundary quality for segmentation
- mAP and Recall@K for retrieval
- AUROC, AUPRO, and F1 for anomaly detection

#### Representation Quality

- Linear-probe performance
- Few-shot learning curves
- Nearest-neighbor consistency
- Effective feature rank
- Feature stability under nuisance transformations
- Utility from final, intermediate, and multi-scale features

#### Exploratory Representation Reuse Ratio

For workload \(w\), define normalized retention relative to a dedicated
reference:

\[
\rho_w =
\frac{u_{\mathrm{reuse},w}+\epsilon}
     {u_{\mathrm{dedicated},w}+\epsilon}.
\]

At preregistered threshold \(\tau\):

\[
\mathrm{RRR}_{\tau}
=
\frac{1}{|\mathcal{W}|}
\sum_{w \in \mathcal{W}}
\mathbb{1}[\rho_w \geq \tau].
\]

The initial target is \(\tau=0.85\), with sensitivity reported at 0.80, 0.90,
and 0.95. RRR must be accompanied by each workload's raw score, normalized
retention, confidence interval, and worst-workload result.

### 3.6 Expected Outcome

The expected result is not uniform superiority over dedicated models. The
likely outcome is a utility profile in which different encoders preserve
different kinds of information. UniSem should identify whether any candidate
provides sufficiently broad reusable utility and which workload properties
limit reuse.

### 3.7 Group A Success Criteria

Group A passes its minimum gate when:

- One frozen encoder reaches \(\mathrm{RRR}_{0.85} \geq 0.80\), equivalent to
  at least four of five workloads meeting the retention threshold.
- No workload result is omitted from the aggregate.
- Few-shot adaptation improves consistently over non-transfer controls.
- Results remain qualitatively stable across seeds and reasonable head
  capacities.

Failure to pass does not invalidate the project. It narrows the representation
scope and may motivate hierarchical or partially specialized infrastructure.

## 4. Compression Experiments

### 4.1 Research Question

Can reusable semantic utility survive compression?

### 4.2 Compression Pipeline

```text
Foundation Encoder
        |
        v
Distillation
        |
        v
Structured Pruning
        |
        v
Quantization
```

The selected Group A encoder is the primary teacher. Compression must be
evaluated after each stage and after the full pipeline.

### 4.3 Measurements

- Task utility retention on every Group A workload
- Representation drift on paired inputs
- Parameter, storage, and FLOP compression ratios
- Peak memory and activation-memory reduction
- Latency and energy where hardware execution is available
- Semantic Utility Retention (SUR)

For compressed representation \(c\), uncompressed baseline \(b\), and workload
\(w\):

\[
\mathrm{SUR}_w =
\frac{u_{c,w}+\epsilon}{u_{b,w}+\epsilon}.
\]

Aggregate SUR is:

\[
\mathrm{SUR}
=
\exp\left(
\sum_{w \in \mathcal{W}}\eta_w\log(\mathrm{SUR}_w)
\right),
\qquad
\sum_w\eta_w=1.
\]

SUR is exploratory and must be reported with per-workload results. A high SUR
from a weak uncompressed representation is not sufficient evidence.

### 4.4 Representation Drift

No single drift metric is assumed to be authoritative. Planned diagnostics
include:

- Cosine distance between paired features
- Centered Kernel Alignment between feature matrices
- Neighborhood-overlap preservation
- Effective-rank change
- Layer-wise feature drift
- Prediction disagreement under fixed heads

Drift metrics will be evaluated for correlation with workload-level utility
loss.

### 4.5 Planned Ablations

| Ablation ID | Factor | Planned levels | Scientific purpose |
|---|---|---|---|
| B-DISTILL-LOSS | Distillation objective | Feature matching, relational matching, multi-layer matching, logits where available | Identify which teacher signals preserve broad reuse |
| B-DISTILL-DATA | Distillation data | Task-balanced, unlabeled mixed-domain, application-specific | Test whether compression data diversity affects reuse |
| B-STUDENT | Student capacity | At least three parameter or width levels | Identify capacity thresholds and scaling behavior |
| B-PRUNE-TYPE | Structured pruning unit | Channel, block, layer, attention head, token where supported | Compare hardware-relevant structural removal |
| B-PRUNE-RATIO | Pruning ratio | Proposed 10%, 25%, 50%, 75% structure removal | Map utility and drift curves |
| B-PRUNE-CRITERION | Saliency criterion | Magnitude, activation, reuse-aware multi-workload | Test whether reuse-aware criteria preserve broader utility |
| B-QUANT-METHOD | Quantization method | Post-training, quantization-aware training | Separate calibration limits from retraining benefits |
| B-QUANT-PRECISION | Precision | FP16/BF16, INT8, INT4 where supported | Identify numerical sensitivity and hardware support |
| B-CALIBRATION | Calibration data | In-domain, mixed-domain, limited calibration | Test calibration bias and domain sensitivity |
| B-ORDER | Pipeline order | Distill-prune-quantize; feasible alternative orders | Determine whether operation order affects preservation |
| B-HEAD | Downstream consumer | Fixed original heads; retrained lightweight heads | Separate representation loss from consumer readaptation |
| B-LAYER | Representation layer | Final, intermediate, multi-scale | Identify layers most robust to compression |

### 4.6 Expected Outcome

Moderate compression is expected to preserve most reusable utility, while
aggressive capacity or precision reduction may cause nonlinear degradation.
Dense prediction and anomaly detection may be more sensitive than
classification. The experiment should identify the compression boundary, not
force a predetermined compression target.

### 4.7 Group B Success Criteria

Group B passes its minimum gate when at least one compressed operating point:

- Achieves at least a 2x encoder storage reduction.
- Achieves aggregate \(\mathrm{SUR} \geq 0.90\).
- Retains at least 0.80 utility on every workload relative to the
  uncompressed representation.
- Produces a measured memory, latency, or energy benefit on supported hardware.

Compression configurations that meet theoretical ratios but produce no
hardware benefit are reported but do not pass the deployment-relevant gate.

## 5. Cross-Application Reuse Experiments

### 5.1 Research Question

Can one representation be reused across different edge applications?

### 5.2 Fixed-Encoder Requirement

Traffic monitoring, industrial inspection, retail analytics, and security
surveillance must use the same encoder checkpoint, input contract, feature
layout, and representation version. Only lightweight heads may change.
Application-specific encoder copies or full encoder fine-tuning are diagnostic
baselines and do not count as direct reuse.

### 5.3 Planned Application Matrix

| Experiment ID | Application | Proposed datasets | Example workload | Primary metrics |
|---|---|---|---|---|
| C-TRAFFIC | Traffic monitoring | BDD100K; Cityscapes; UA-DETRAC if licensing permits | Vehicle and pedestrian detection or segmentation under weather and time shifts | mAP, mIoU, domain retention, adaptation cost |
| C-INDUSTRIAL | Industrial inspection | MVTec AD; VisA | Defect and anomaly detection across product categories | AUROC, AUPRO, F1, category transfer |
| C-RETAIL | Retail analytics | SKU-110K; RPC where accessible | Dense product detection, counting, or retrieval | mAP, counting MAE, Recall@K |
| C-SECURITY | Security surveillance | ShanghaiTech; Avenue; UCF-Crime subject to ethics and access review | Scene or event anomaly detection under camera and environment changes | Frame/video AUROC, event recall, false alarms per hour |

Security-surveillance experiments require an explicit ethics, privacy, and
dataset-governance review. The project must avoid identity recognition or
demographic profiling unless separately justified and approved.

### 5.4 Adaptation Protocol

For each application:

1. Freeze the shared encoder.
2. Train a lightweight application head under fixed parameter budgets.
3. Evaluate full-data and few-shot regimes.
4. Record labels, trainable parameters, training compute, wall-clock time,
   storage, and engineering steps.
5. Compare with a dedicated application model and an application-specific
   fine-tuned encoder.
6. Test at least one domain or operating-condition shift where possible.

### 5.5 Measurements

- Application-level task utility
- Transferability under full-data and few-shot adaptation
- Number of trainable parameters
- Labeled examples and annotation cost
- Training compute and wall-clock time
- Application-specific storage
- Performance retention relative to dedicated application models
- Cross-Application Reuse Score (CARS)

For application \(a\), let \(\rho_a\) be utility retention and \(e_a\) be
normalized adaptation efficiency. CARS combines the geometric mean of these
terms with threshold coverage:

\[
\mathrm{CARS}
=
\exp\left(
\sum_{a \in \mathcal{A}}\lambda_a
\log\left((\rho_a+\epsilon)^\gamma
\,(e_a+\epsilon)^{1-\gamma}\right)
\right)
\times
\mathrm{Coverage}_{\tau}.
\]

The default exploratory setting is \(\gamma=0.7\), prioritizing utility over
adaptation savings, and \(\tau=0.85\). All weights and cost normalizations must
be frozen before final evaluation.

### 5.6 Expected Outcome

The same representation is expected to support multiple applications with
different lightweight heads, but utility and adaptation cost will vary by
domain. Industrial anomaly detection and dense retail scenes may expose
different limitations from traffic and surveillance workloads.

### 5.7 Group C Success Criteria

Group C passes its minimum gate when:

- All four applications use the identical frozen encoder checkpoint.
- At least three of four applications retain at least 85% of
  dedicated-reference normalized utility.
- Median application-specific trainable parameters and storage are at least
  50% lower than dedicated alternatives.
- Median adaptation compute is at least 30% lower than full application-model
  training.
- Cross-domain tests retain at least 80% of the corresponding in-domain
  normalized utility for at least three applications.
- CARS is reported with all application-level utility and cost components.

## 6. Edge-System Evaluation

### 6.1 Research Question

Does representation reuse reduce deployment cost?

### 6.2 Compared Deployments

Traditional:

```text
Application A -> Dedicated Model A
Application B -> Dedicated Model B
Application C -> Dedicated Model C
Application D -> Dedicated Model D
```

UniSem:

```text
Input -> Shared Semantic Encoder -> Lightweight Application Heads
```

Both deployments must serve the same application portfolio at equivalent
input resolution, quality constraints, and workload arrival patterns.

### 6.3 Evaluation Scenarios

- One active application
- Multiple applications consuming the same input
- Concurrent applications with independent arrival times
- Cold start
- Warm steady state
- Feature caching enabled and disabled
- Uncompressed UniSem encoder
- Selected compressed UniSem operating point
- At least one CPU/GPU-class edge platform and one accelerator-class platform,
  subject to hardware availability

### 6.4 Measurements

#### Memory

- Model resident memory
- Peak process memory
- Activation memory
- Feature-cache memory

#### Storage

- Serialized encoder and heads
- Runtime and auxiliary artifact footprint
- Application update size

#### Latency and Throughput

- Median, p90, and p99 latency
- Cold-start latency
- Encoder and head latency separately
- Completed application outputs per second

#### Energy

- Energy per input
- Energy per completed application portfolio
- Average and peak power
- Thermal throttling behavior

#### Deployment Complexity

- Number of model artifacts
- Number of hardware-specific builds
- Configuration and preprocessing variants
- Application onboarding steps
- Update volume
- Compatibility and validation cases

### 6.5 Utility-Efficiency Score

For measured cost \(j\):

\[
G_{\mathrm{eff}}
=
\exp\left(
\sum_{j \in \mathcal{C}}\alpha_j
\log
\frac{C_{\mathrm{traditional},j}+\epsilon}
     {C_{\mathrm{UniSem},j}+\epsilon}
\right).
\]

Let \(U_{\mathrm{reuse}}\) be the geometric mean of application utility
retention. The exploratory system-level score is:

\[
\mathrm{UES}
= U_{\mathrm{reuse}} \times G_{\mathrm{eff}}.
\]

UES is specific to the device, runtime, workload portfolio, cost weights, and
quality constraints. Raw measurements remain primary.

### 6.6 Expected Outcome

UniSem is expected to provide the largest benefit when several applications
consume the same input or when encoder storage dominates application-head
storage. A dedicated model may remain preferable for a single isolated
application or when feature movement and scheduling overhead dominate.

### 6.7 Group D Success Criteria

Under at least one preregistered multi-application workload, UniSem must:

- Preserve every application's minimum quality constraint.
- Reduce combined model storage by at least 30%.
- Reduce peak memory by at least 20%.
- Reduce energy per completed application portfolio by at least 20%, or reduce
  median portfolio latency by at least 20%.
- Reduce model-artifact count and application update volume.
- Retain the advantage after preprocessing, feature movement, caching, and
  head costs are included.

Results must also identify break-even points where reusable infrastructure is
not advantageous.

## 7. Datasets

All entries are proposed until access, license, contamination, ethics, and
compute reviews are complete.

| Dataset | Task | Domain | Status | Priority |
|---|---|---|---|---|
| CIFAR-100 | Classification pilot | General objects | Proposed pilot | Medium |
| ImageNet-1K | Classification | General objects | Proposed | High |
| COCO 2017 | Detection | General scenes | Proposed | High |
| ADE20K | Semantic segmentation | Indoor and outdoor scenes | Proposed | High |
| Cityscapes | Segmentation and transfer | Urban driving | Proposed | High |
| Revisited Oxford | Image retrieval | Landmarks | Proposed | High |
| Revisited Paris | Image retrieval | Landmarks | Proposed | High |
| Flickr30k | Cross-modal retrieval | General image-text | Optional | Low |
| MVTec AD | Anomaly detection | Industrial inspection | Proposed | High |
| VisA | Anomaly detection and external validation | Industrial inspection | Proposed | High |
| BDD100K | Detection, segmentation, domain shift | Traffic monitoring | Proposed | High |
| UA-DETRAC | Detection and tracking | Traffic surveillance | Access review | Medium |
| SKU-110K | Dense detection and counting | Retail shelves | Proposed | High |
| RPC | Product recognition | Retail products | Access review | Medium |
| ShanghaiTech | Video anomaly detection | Security surveillance | Ethics review | Medium |
| Avenue | Video anomaly detection | Security surveillance | Ethics review | Medium |
| UCF-Crime | Video anomaly detection | Security surveillance | Ethics and access review | Low |
| DomainNet | Cross-domain transfer | Multiple visual domains | Optional validation | Medium |
| ImageNet-V2/R/A/Sketch | Robustness and domain shift | General objects | Optional validation | Medium |

The final dataset set should favor reproducibility and domain diversity while
avoiding unnecessary benchmark proliferation.

## 8. Baselines

### 8.1 Dedicated Task and Application Models

Use task-standard or application-standard models as specialization references.
The exact architectures must be selected before final evaluation and matched
for input resolution, data, and training budget where possible.

Required categories include:

- Dedicated classification model
- Dedicated object detector
- Dedicated semantic-segmentation model
- Dedicated retrieval model
- Dedicated anomaly-detection model
- Dedicated model for each Group C application

### 8.2 Foundation Representation Baselines

- DINOv2 with frozen features
- SigLIP2 with frozen features
- CLIP with frozen features

For each representation, report:

- Checkpoint and pretraining disclosure
- Parameter count
- Input resolution
- Feature layer and dimension
- Preprocessing
- Frozen, linear-probe, and lightweight-head results

### 8.3 Adaptation Baselines

- Randomly initialized encoder plus task head
- Conventional supervised pretrained encoder
- Frozen foundation encoder
- Partial fine-tuning
- Full fine-tuning
- Adapter or low-rank adaptation where architecture permits

Only frozen-encoder results count as direct representation reuse. Adapted
encoders measure the cost and benefit of specialization.

### 8.4 Compression Baselines

- Uncompressed foundation encoder
- Distilled-only variants
- Structured-pruned-only variants
- Quantized-only variants
- Distilled and pruned variants
- Distilled, pruned, and quantized variants
- Size-matched models trained without the foundation teacher

### 8.5 System Baselines

- One dedicated model per application
- One reusable uncompressed encoder plus lightweight heads
- One reusable compressed encoder plus lightweight heads
- Shared encoder without feature caching
- Shared encoder with feature caching

## 9. Success Criteria

These are minimum program-level criteria, not claims of state-of-the-art
performance.

| Criterion | Minimum target | Required evidence |
|---|---|---|
| Representation reuse | At least four of five Group A workloads meet 85% normalized utility retention with one frozen encoder | Per-workload results, \(\mathrm{RRR}_{0.85} \geq 0.80\), confidence intervals |
| Compression robustness | At least one 2x-smaller operating point achieves aggregate \(\mathrm{SUR} \geq 0.90\) and no workload below 0.80 retention | Stage-wise utility, drift, compression ratios, hardware measurement |
| Cross-application reuse | One identical encoder supports all four applications; at least three meet 85% utility retention with lightweight heads | Application retention, adaptation cost, CARS components |
| Cross-domain transfer | At least three applications retain 80% of in-domain normalized utility on a held-out domain or operating condition | Source/target results, uncertainty, domain-shift description |
| Edge deployment | At least 30% storage and 20% peak-memory reduction, plus 20% energy or latency improvement under one multi-application workload | Raw device measurements, configuration metadata, utility constraints, UES |
| Scientific validity | Results remain interpretable after scale, head capacity, contamination, and adaptation-budget controls | Ablations, preregistration, negative results, sensitivity analyses |

Program-level success requires the representation-reuse criterion and at least
two of the three downstream evidence criteria: compression robustness,
cross-application reuse, and edge deployment. This prevents compression or
systems optimization from substituting for the central scientific claim.

## 10. Experiment Tracking Table

Status values should use: `planned`, `ready`, `running`, `blocked`, `complete`,
or `abandoned`. Every completed row should link to its configuration, raw
results, and analysis artifact when those repository structures exist.

| Experiment ID | Objective | Status | Owner | Result | Notes |
|---|---|---|---|---|---|
|  |  |  |  |  |  |
|  |  |  |  |  |  |
|  |  |  |  |  |  |
|  |  |  |  |  |  |
|  |  |  |  |  |  |
|  |  |  |  |  |  |
|  |  |  |  |  |  |
|  |  |  |  |  |  |

## Experiment Governance

This file is the authoritative planning document for UniSem experiments.
Changes to primary datasets, metrics, thresholds, or comparison protocols
should be recorded with:

- Date
- Rationale
- Affected experiment IDs
- Whether results had already been observed
- Expected effect on interpretation

The research roadmap defines the broader scientific direction. This plan
defines the operational evidence required to support or reject that direction.
