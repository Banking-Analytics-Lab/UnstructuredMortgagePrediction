# UnstructuredMortgagePrediction

Code for the paper:

> Mahsa Tavakoli, Rohitash Chandra, Cristián Bravo (2027). Capsule network–based multimodal fusion for mortgage risk assessment from unstructured data sources. *Expert Systems with Applications*, 334, 134887. <https://doi.org/10.1016/j.eswa.2026.134887>

Preprint: [arXiv:2510.22987](https://arxiv.org/abs/2510.22987).

Traditional mortgage risk models rely on structured financial data, which is often costly and
inaccessible. The paper proposes a multimodal deep learning framework that predicts mortgage
default risk from publicly available unstructured data sources: financial news, LiDAR imagery and
sentiment scores. Its fusion model, FusionCapsNet, is a capsule-inspired architecture that keeps
spatial, contextual and modality-specific detail. Each modality has its own encoder (BERT for the
news text, VGG for the LiDAR images, an MLP for the sentiment scores), and adaptive weighting with
routing-by-agreement emphasises the reliable modalities and reduces noise. For interpretability,
the paper shows sentiment heat maps (how positive and negative news affects the predicted risk) and
Grad-CAM maps of the LiDAR images (geospatial and neighbourhood risk patterns). This repository
holds the training scripts for FusionCapsNet and for the three baseline fusion strategies of the
paper: addition, concatenation and cross-attention. The code for the interpretability figures is
not in this repository.

Repository contents:

| Path | Content |
|---|---|
| `code/capsule.py` | FusionCapsNet |
| `code/crossattention.py` | Cross-attention fusion baseline |
| `code/addition.py` | Addition fusion baseline |
| `code/concat.py` | Concatenation fusion baseline |
| `Plot/cross_attention_convergence.png` | Training and validation loss of the cross-attention baseline over 100 epochs |
| `Plot/cross_attention_convergence` | Short note on that plot |
| `data/README.md` | Data sources and the files the scripts expect |
| `requirements.txt` | Python packages imported by the scripts |

## Requirements

The Python and package versions used for the paper were not recorded. The scripts were run in
Google Colab. To install the packages that the scripts import in another environment:

```bash
pip install -r requirements.txt
```

Notes on the environment:

- The scripts load BERT with `TFBertModel`, the TensorFlow class of `transformers`. That class is
  not in `transformers` 5 (`modeling_tf_bert.py` is absent from the `v5.0.0` tag), so use a 4.x
  release.
- The scripts load BERT weights with `from_pt=True`, which also needs PyTorch installed.
- The paper reports approximate training times on one T4 GPU (Table 5): about 2.2 hours for
  FusionCapsNet, 3.5 hours for cross-attention, 1.5 hours for addition and 1.5 hours for
  concatenation.

## Data

The data are not in this repository. The paper uses residential mortgage data for the Netherlands
from the European DataWarehouse (EUDW), news from Seeking Alpha, sentiment scores computed from
that news, and LiDAR elevation images of the Netherlands (AHN) from the Google Earth Engine Data
Catalog. `data/README.md` gives the sources and the file names the scripts expect.

The scripts read two environment variables:

| Variable | Default | Use |
|---|---|---|
| `DATA_DIR` | `data` | Folder with the CSV files and the LiDAR images (`int.zip` or `int/`) |
| `OUTPUT_DIR` | `outputs` | Folder where each script writes its results, in a subfolder per model |

Both defaults are relative to the working directory.

## Training

Each script is a Google Colab notebook exported to Python. It mounts Google Drive
(`drive.mount`), installs packages with `!pip install` lines, loads the data, trains one model and
writes the results. The `!` lines are notebook shell commands, so `python code/capsule.py` stops
with a syntax error. Run a script in Colab: in a new notebook, set the two variables in the first
cell, then paste the script into the next cell.

```python
import os
os.environ["DATA_DIR"] = "<folder with the data files>"
os.environ["OUTPUT_DIR"] = "<folder for the results>"
```

| Model in the paper | Script | Results folder |
|---|---|---|
| FusionCapsNet | `code/capsule.py` | `$OUTPUT_DIR/run_cap3/` |
| Cross-attention | `code/crossattention.py` | `$OUTPUT_DIR/run_cross/` |
| Addition | `code/addition.py` | `$OUTPUT_DIR/run_addition/` |
| Concatenation | `code/concat.py` | `$OUTPUT_DIR/run_concat/` |

Each script trains for at most 100 epochs on one data split.

## Evaluation

Evaluation runs at the end of each training script. The script predicts on the validation split
and writes these files to its results folder:

- `history.csv`: the training history per epoch.
- `classification_report.csv`: the classification report on the validation split.
- `predictions.csv`: true label, predicted label and the two class probabilities for each
  validation case.
- `model_keras`: the trained Keras model.

The scripts also write a model diagram to the working directory (`model_add.png` for addition,
`model.png` for the others). `code/capsule.py` also saves its best weights to
`best_kmodel.weights.h5` in the working directory. The scripts build a test split but do not score
it.

## Pretrained models

Not released. The repository does not state a reason.

## Results

FusionCapsNet significantly outperforms the baseline fusion strategies (addition, concatenation
and cross-attention); the paper tests this with paired t-tests over 30 runs. The values below are
from Table 5 of the paper (arXiv v2): mean and standard deviation over 30 repeated runs. The scripts train one run and report validation metrics. The code
for the 30 runs, the pAUC and the paired t-tests is not in this repository.

| Table or figure in the paper | Command | Expected result |
|---|---|---|
| Table 5, FusionCapsNet | `code/capsule.py` (see Training) | AUC 0.692 ± 0.021, pAUC 0.696 ± 0.014, F1 0.552 ± 0.018 |
| Table 5, Cross-attention | `code/crossattention.py` (see Training) | AUC 0.607 ± 0.030, pAUC 0.649 ± 0.011, F1 0.493 ± 0.025 |
| Table 5, Addition | `code/addition.py` (see Training) | AUC 0.622 ± 0.013, pAUC 0.664 ± 0.017, F1 0.511 ± 0.002 |
| Table 5, Concatenation | `code/concat.py` (see Training) | AUC 0.668 ± 0.021, pAUC 0.682 ± 0.019, F1 0.547 ± 0.012 |
| Tables 1 to 4 (sentiment scores, text similarity, LiDAR versus postal code, unimodal models) | Not documented | Not documented |
| Figure 9 (loss curves of the cross-attention baseline) | Not documented. `code/crossattention.py` writes the losses per epoch to `history.csv`. | `Plot/cross_attention_convergence.png` shows the loss curves of one run |
| Figure 10 (sentiment heat map) | Not documented | Not documented |
| Figure 11 (Grad-CAM maps) | Not documented | Not documented |

## Future work

- Extend the datasets to additional regions and borrower groups.
- Incorporate social media data and macroeconomic indicators.
- Explore generative models and transfer or self-supervised learning for richer feature
  extraction.

## Citation

```bibtex
@article{tavakoli2027capsule,
  title   = {Capsule network--based multimodal fusion for mortgage risk assessment from unstructured data sources},
  author  = {Tavakoli, Mahsa and Chandra, Rohitash and Bravo, Cristián},
  year    = {2027},
  journal = {Expert Systems with Applications},
  volume  = {334},
  pages   = {134887},
  doi     = {10.1016/j.eswa.2026.134887},
}
```

`CITATION.cff` holds the same reference for GitHub's "Cite this repository" button.

## Licence

Apache-2.0. See `LICENSE`.

## Acknowledgements

As stated in the paper: the first and last authors acknowledge the support of the NSERC Discovery
Grant program [RGPIN-2020-07114]. The research was funded in part by the Canada Research Chairs
program [CRC-2024-00192]. Compute Ontario, Calcul Québec and the Digital Research Alliance of
Canada provided computing support.
