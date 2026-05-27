# Image Matching with FAISS

I learnt about this while working on a task where I had to find visually similar images across two scientific illustration libraries — ~1,723 images in Library A and ~14,521 in Library B.
Brute-forcing LLM-based comparison across all combinations was ~25M pairs. Not feasible cost-wise. Hence the need for a smarter candidate retrieval pipeline.

**Pipeline:**  
```
Two parallel signals -> merged candidate set -> GPT visual judge.
```
*Two Signals*
1. Name-based matching: rapidfuzz token_set_ratio on cleaned filenames. Zero compute. Catches obvious duplicates by name alone.
2. CLIP + FAISS - Encodes all images with CLIP ViT-L/14 -> builds IndexFlatIP index -> retrieves top-4 nearest neighbors per Library A image.

Both signals merged and deduplicated per image before passing to GPT model.

**Why IndexFlatIP and not IndexFlatL2?**  
CLIP embeddings are L2-normalized by default. On normalized vectors, inner product = cosine similarity. IndexFlatIP is the correct choice here. Using IndexFlatL2 on normalized embeddings gives subtly wrong rankings.

**Why Flat index and not IVFPQ or HNSW?**  
At ~14K vectors, exact brute-force search is fast enough. Approximate indexes trade recall for speed — that tradeoff only makes sense at 1M+ vectors. Here, exact recall at retrieval stage was worth more than any speed gain.

**Earlier Experiment — ResNet50**  
First iteration used ResNet50 (classification head removed) -> 2048-dim feature vectors -> IndexFlatL2 -> threshold of 200 (SME-calibrated).
This was superseded by CLIP because ResNet50 features are generic (ImageNet-trained) and miss semantic similarity. Say, two images of a neuron in different illustration styles — ResNet50 may miss the match, CLIP catches it.
