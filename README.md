
# Unstructured Mortgage Risk Prediction

This repository contains the code and data for the paper:  
**Capsule Network–Based Multimodal Fusion for Mortgage Risk Assessment from Unstructured Data Sources**.

---

## Overview
Traditional mortgage risk models rely on **structured financial data**, which is often costly and inaccessible.  
This project introduces a **novel multimodal deep learning framework** that uses **publicly available unstructured data sources**—financial news, LiDAR imagery, and sentiment scores—to predict mortgage default risk.

---

## Key Contributions
- **FusionCapsNet Architecture**: Capsule-inspired fusion model that preserves **spatial, contextual, and modality-specific** details.  
- **Unimodal Encoders**:
  - **BERT** → Textual news data  
  - **VGG** → LiDAR imagery  
  - **MLP** → Sentiment-based numeric features  
- **Adaptive weighting & routing-by-agreement** to emphasize reliable modalities while reducing noise.  
- **Interpretability** with:
  - Sentiment heatmaps showing how positive/negative news influences risk.  
  - GradCAM visualizations highlighting geospatial/neighborhood risk patterns.  

---

## Results
FusionCapsNet significantly outperforms baseline fusion strategies (addition, concatenation, cross-attention).


## Repository Structure
- `src/` → Model architecture and training scripts  
- `data/` → Sample datasets and sources  
- `notebooks/` → Experiments and analysis  
 

---

## Future Work
- Extend datasets to additional regions and borrower groups.  
- Incorporate **social media data & macroeconomic indicators**.  
- Explore **generative models, transfer/self-supervised learning** for richer feature extraction.  

---




