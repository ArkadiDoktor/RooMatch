RooMatch

Room Re-Identification via Content-Preserving Image Retrieval

Project Motivation

Room re-identification is a challenging computer vision problem where visually different images may represent the same physical space. In real-world applications such as real estate platforms, interior design tools, and digital asset management, rooms often undergo changes in furniture, decor, or occupancy while their structural layout remains unchanged.

RooMatch is motivated by the question:

Can modern vision foundation models recognize the same room despite significant appearance-level changes, without relying on explicit geometric supervision?

Problem Statement

Given a query image of a room, retrieve the Top-K most similar images from a database such that all retrieved images correspond to the same physical room.

Key constraints:

Room geometry is fixed (walls, windows, doors, camera position)

Only movable interior elements may change

Evaluation is performed under a strict retrieval criterion

This formulation emphasizes robust spatial understanding rather than superficial visual similarity.

Visual Abstract

The RooMatch pipeline consists of:

Controlled Dataset Construction
Real room images are augmented using inpainting-based generation to create realistic variations while preserving structure.

Embedding Extraction
Images are encoded using a vision foundation model to produce normalized embeddings.

Image Retrieval & Evaluation
Nearest-neighbor search is performed in embedding space, followed by strict evaluation.

Visual summaries of experiments and results are available in the Visuals/ directory.

Datasets Used or Collected
Intermediate Dataset (interm_dataset/)

50 real room images

10 synthetic variants per room

Total: 550 images

Used for early validation and experimentation

Complete Dataset (complete_dataset/)

1000 real room images

10 synthetic variants per room

Total: 11,000 images

Dataset structure:

complete_dataset/
├── real/                # room_1.jpg ... room_1000.jpg
├── synthetic/
│   ├── room_1/
│   │   ├── syn_00.png ... syn_09.png
│   └── ...
├── labels.csv
└── labels_fixed.csv


Each image is associated with a room_id used for retrieval evaluation.

Data Augmentation and Generation Methods

Synthetic images are generated using Stable Diffusion Inpainting, with carefully designed prompts to ensure:

Identical room geometry

No addition or removal of structural elements

Only movable objects are modified

Augmented attributes include:

Furniture

Decor

Plants

People

Clutter

Material appearance

A strong negative prompt prevents architectural or camera changes.

Input / Output Examples

Input:
A single image of a room (real or synthetic)

Output:
Top-K retrieved images ranked by cosine similarity in embedding space

Goal:
All retrieved images must share the same room_id as the query image.

Models and Pipelines Used
Feature Extraction

Model: DINOv2 ViT-B/14

Pretrained vision foundation model

Used as a frozen embedding extractor

Retrieval

Embeddings are L2-normalized

Similarity computed using cosine distance

Nearest neighbors retrieved via sklearn

Training Process and Parameters

No supervised fine-tuning is required for the base model

All experiments focus on embedding quality and retrieval behavior

Image preprocessing:

Resize to 224×224

Center crop

ImageNet normalization

Metrics
Strict Recall@K (Primary Metric)

A retrieval is considered correct only if all Top-K results belong to the same room.

\text{Strict Recall@K} = \frac{\text{# queries where all Top-K match room\_id}}{\text{total # queries}}

This metric enforces a highly conservative and application-relevant success criterion.

Results

Quantitative results are provided in the Results/ directory:

Base-Model_results.csv

Embedding-Seprability_results.csv

Fine-Tuned-Model_results.csv

Interm_results.csv

Visual performance summaries are available in the Visuals/ directory.

Repository Structure
├── Code/
│   ├── Complete_code/
│   │   ├── Generation_notebook.ipynb
│   │   └── Evaluation_notebook.ipynb
│   └── Interm_code/
│       ├── Room_generation.ipynb
│       └── Room_evaluation.ipynb
│
├── Results/
│   ├── Base-Model_results.csv
│   ├── Embedding-Seprability_results.csv
│   ├── Fine-Tuned-Model_results.csv
│   └── Interm_results.csv
│
├── Slides/
│   ├── Final_slides/
│   │   ├── RooMatch-Final.pdf
│   │   └── RooMatch-Final.pptx
│   ├── Interm_slides/
│   │   ├── RooMatch.pdf
│   │   └── RooMatch.pptx
│   └── Proposal_slides/
│       ├── AI-Interior-Design-Proposal.pdf
│       └── AI-Interior-Design-Proposal.pptx
│
├── Visuals/
│   ├── Base-Model_visuals.png
│   ├── Embedding-Seprability_visuals.png
│   ├── Fine-Tuned-Model_visuals.png
│   └── Interm_visuals.png
│
├── complete_dataset/
├── interm_dataset/
└── README.md

Team Members

[Your Name]
