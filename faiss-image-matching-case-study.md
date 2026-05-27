# Image Matching with FAISS — Cross-Library Scientific Illustrations

## Problem
Find visually similar images across two scientific illustration libraries — ~1,723 images in Library A and ~14,521 in Library B. Brute-forcing LLM-based visual comparison across all combinations (~25M pairs) was cost-prohibitive.

## Pipeline — Cost-Progressive Funnel

```
ResNet50 + FAISS   →   CLIP matching   →   GPT-4.1 visual judge
  (coarse filter)       (refinement)        (final scoring)
```

Each stage gets progressively more expensive and accurate. FAISS's job was purely to shrink the candidate space cheaply.

## FAISS Implementation

- **Index:** `IndexFlatL2` — exact search, no approximation
- **Embeddings:** ResNet50 pretrained, classification head removed → 2048-dim feature vectors
- **Query:** For each Library A image, retrieve top-3 nearest neighbors from Library B
- **Threshold:** L2 distance < 200 flagged as potential match — calibrated via SME evaluation

## Why IndexFlatL2 and not IVFPQ or HNSW?

At ~14K vectors, brute-force search is fast enough. Approximate indexes trade recall for speed — that tradeoff only makes sense at much larger scale (1M+). Here, exact recall at the retrieval stage was worth more than the marginal speed gain.

## What I'd revisit

- ResNet50 features are generic (ImageNet-trained). A CLIP embedding at the retrieval stage might give better semantic alignment for scientific illustrations, potentially reducing the candidate set passed to GPT-4.1.
- The distance threshold of 200 was SME-calibrated — a precision/recall curve against a labeled pair set would make this more defensible at scale.
