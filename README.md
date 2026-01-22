# RooMatch: Image Retrieval for Interior Room Re-identification

## Project Motivation

There is currently no ready-to-use dataset for identifying the same physical room with different interior arrangements. RooMatch addresses this gap by providing a controlled synthetic dataset and an image retrieval system to recognize rooms despite changes in furniture, decor, or objects. The main use cases include detecting fraud on real estate websites and Airbnb listings, as well as finding the lowest price across different platforms by identifying the same room listed multiple times.

## Problem Statement

Given a query image of a room, the goal is to retrieve all images real or synthetic that depict the same physical room. The challenge is to ignore cosmetic changes (furniture, decor, plants, clutter) while preserving structural identity. This requires models to focus on spatial and semantic cues rather than superficial appearance.

## Visual Abstract

The RooMatch pipeline consists of:

1. Generating 10 synthetic variants per real room image.
2. Embedding all images using DINOv2 ViT-B/14.
3. Constructing a nearest-neighbors index in embedding space.
4. Evaluating retrieval using **Strict Recall@K**, where all top-K retrieved images must belong to the same room.

Visual summaries of embeddings, separability, and retrieval performance are stored in the **Visuals/** directory.

## Datasets Used / Collected

### 1. Complete Dataset

* **1,000 real room images** (`real/room_1.jpg` … `room_1000.jpg`)
* **10 synthetic variants per room** (`synthetic/room_1/syn_00.png` … `syn_09.png`)
* **Labels CSV**: `labels.csv` and `labels_fixed.csv` contain image paths and room IDs

Structure:

```
complete_dataset/
 ├── real/
 ├── synthetic/
 │    ├── room_1/
 │    └── room_1000/
 ├── labels.csv
 └── labels_fixed.csv
```

### 2. Intermediate Dataset

* **50 real room images** (`real/room_0.jpg` … `room_49.jpg`)
* **10 synthetic variants per room** (`synthetic/room_0/syn_00.png` … `syn_09.png`)

Mirrors the complete dataset at smaller scale.

## Data Augmentation and Generation Methods

Synthetic images are generated with **Stable Diffusion inpainting**, ensuring only movable objects are changed while the room structure remains intact:

* Furniture, decor, people, plants, and clutter are modified.
* Walls, windows, doors, floors, ceilings, and camera positions remain identical.
* Each variant uses a unique combination of attributes and random masks to enhance diversity.

Ground truth labels map each image to its corresponding room ID.

## Input / Output Examples

**Input:** A query image of a room (real or synthetic).

**Output:** Top-K images from the dataset corresponding to the same physical room.

* Retrieval quality is measured using **Strict Recall@K**, counting only queries where all top-K neighbors match the same room.

Example visualization: query `real/room_42.jpg` retrieves `synthetic/room_42/syn_00.png` … `syn_09.png` plus `real/room_42.jpg`.

## Models and Pipelines Used

1. **Base DINOv2 Embeddings**

   * Model: `facebookresearch/dinov2_vitb14`
   * Outputs normalized 768-dimensional embeddings

2. **Nearest-Neighbors Index**

   * sklearn `NearestNeighbors` with cosine distance
   * Retrieves top-K images per query

3. **Optional Fine-Tuning**

   * DINOv2 embeddings can be fine-tuned to improve separability between rooms

4. **Visualization Pipelines**

   * Strict Recall@K plots
   * Embedding separability analysis
   * Random query retrieval visualization

## Training Process and Parameters

* Embeddings are **frozen** (no training) for standard evaluation
* Fine-tuning (optional) uses the same embedding model with cosine similarity optimization
* Retrieval evaluation uses a nearest-neighbors index with k up to 10

## Metrics

* **Strict Recall@K**: Fraction of queries for which all top-K retrieved images belong to the same room
* Evaluated for K=1 to 10
* Embedding separability visualized via dimensionality reduction and clustering plots

## Results

Results are provided as CSVs under the `Results/` directory:

* `Base-Model_results.csv`
* `Embedding-Separability_results.csv`
* `Fine-Tuned-Model_results.csv`
* `Interm_results.csv`

Observations:

* DINOv2 embeddings alone achieve high retrieval performance.
* Fine-tuning slightly improves top-K strict recall.
* Embedding separability visualization demonstrates clustering by room.

Visual comparisons are stored in `Visuals/`:

* `Base-Model_visuals.png`
* `Embedding-Separability_visuals.png`
* `Fine-Tuned-Model_visuals.png`
* `Interm_visuals.png`

## Repository Structure

```
Code/
 ├── Complete_code/
 │    ├── Generation_notebook.ipynb
 │    └── Evaluation_notebook.ipynb
 └── Interm_code/
      ├── Room_generation.ipynb
      └── Room_evaluation.ipynb

complete_dataset/
 ├── real/
 ├── synthetic/
 ├── labels.csv
 └── labels_fixed.csv

interm_dataset/
 ├── real/
 ├── synthetic/
 └── labels.csv

Results/
 ├── Base-Model_results.csv
 ├── Embedding-Separability_results.csv
 ├── Fine-Tuned-Model_results.csv
 └── Interm_results.csv

Slides/
 ├── Proposal_slides/
 ├── Interm_slides/
 └── Final_slides/

Visuals/
 ├── Base-Model_visuals.png
 ├── Embedding-Separability_visuals.png
 ├── Fine-Tuned-Model_visuals.png
 └── Interm_visuals.png
```

## Team Members

* *[Arkadi Doktorovich]*
* *[Elia Meerson]*
* *[Shai Gigi]*

---

This repository accompanies the RooMatch project on image retrieval for interior room and is intended for research and educational purposes.

