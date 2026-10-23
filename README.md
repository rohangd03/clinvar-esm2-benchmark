# Benchmarking Protein Language Model Scores for Classifying ClinVar Missense Variants Under Gene-Disjoint Evaluation

**Author:** Rohan Ghosh Dastidar  
**Affiliation:** Department of Biotechnology, Indian Institute of Technology Kharagpur, India  
**Date:** October 23, 2026

## Overview

This project benchmarks the 650M-parameter ESM-2 protein language model for predicting the pathogenicity of human missense variants from ClinVar.

It compares alignment-free, zero-shot masked-marginal log-likelihood scores against a supervised linear probe trained on ESM-2 embedding differences and a BLOSUM62 substitution-matrix baseline.

A key focus is evaluating generalization under gene-disjoint and sequence-homology-disjoint cross-validation, reducing the risk of overly optimistic performance estimates caused by gene or homology leakage.

## Key Results

The reported benchmark contains **66,332 missense variants across 7,339 genes**, with a pathogenic/likely pathogenic prevalence of 38.0%.

| Predictor | AUROC | PR-AUC |
|---|---:|---:|
| BLOSUM62 | 0.663 | 0.504 |
| ESM-2 zero-shot | 0.901 | 0.875 |
| ESM-2 embedding + logistic regression | 0.901 | 0.866 |

Evaluation used five-fold gene-disjoint cross-validation. Additional analyses examined 30% and 50% sequence-identity clustering and long proteins evaluated with centered sequence windows.

These results are reported by the project and have not been independently validated through this repository description.

## Methods

The benchmark workflow includes:

1. **Variant filtering:** Select high-confidence germline missense variants from ClinVar, retaining unambiguous pathogenic/likely pathogenic and benign/likely benign labels.
2. **Transcript-level sequence recovery:** Match variants to canonical UniProt sequences and recover transcript-specific sequences using RefSeq where necessary.
3. **Zero-shot scoring:** Evaluate masked-marginal log-likelihood ratios using `facebook/esm2_t33_650M_UR50D`.
4. **Supervised scoring:** Train regularized logistic regression on 1,280-dimensional ESM-2 embedding-difference vectors.
5. **Baseline comparison:** Evaluate BLOSUM62 substitution scores.
6. **Leakage-resistant evaluation:** Use grouped cross-validation by gene and sequence-identity cluster.
7. **Uncertainty estimation:** Calculate bootstrap confidence intervals and assess gene-level sensitivity.

## Repository Contents

- `playground.ipynb` — exploratory analysis notebook; inspect and clean before publication.
- `cv_results_combined.tsv` — combined cross-validation results.
- `cv_tuned_C.tsv` — selected logistic-regression regularization strengths.
- `bootstrap_auroc_combined.tsv` — AUROC bootstrap results.
- `bootstrap_tuned_combined.tsv` — bootstrap results for the tuned classifier.
- `per_gene_auroc.tsv` — per-gene discrimination analysis.
- `per_gene_top.tsv` — selected per-gene results.
- `clinvar_esm2_scientific_report.pdf` — formatted scientific report.

Additional tables may be included for windowed proteins, sequence-homology clustering, and transcript-recovery analyses.

## Data Sources

- [ClinVar](https://www.ncbi.nlm.nih.gov/clinvar/) — clinical variant interpretations.
- [UniProt](https://www.uniprot.org/) — protein sequences.
- [NCBI RefSeq](https://www.ncbi.nlm.nih.gov/refseq/) — transcript reference sequences.
- [ESM](https://github.com/facebookresearch/esm) — protein language model resources.
- [MMseqs2](https://github.com/soedinglab/MMseqs2) — sequence clustering.

Raw downloads, cached sequences, and large intermediate model arrays are intentionally excluded from version control. Follow the relevant data-source terms and project instructions when retrieving inputs.

## Reproducibility

The intended workflow is to retrieve the required source data, reconstruct transcript-matched protein sequences, compute model scores, perform grouped cross-validation, and generate the result tables.

**Reproducibility status:** Complete execution instructions and dependency versions should be documented alongside the analysis code before claiming that the entire benchmark can be reproduced from a fresh checkout.

## Limitations

- ClinVar contains ascertainment and gene-coverage biases.
- Zero-shot scores are not calibrated posterior probabilities of pathogenicity.
- Performance comparisons depend on the specified variant filters, sequence mapping, model configuration, and evaluation splits.
- The reported results do not by themselves establish clinical utility.

## Citation

If you use this work, cite the associated report:

Ghosh Dastidar, R. (2026). *Benchmarking Protein Language Model Scores for Classifying ClinVar Missense Variants Under Gene-Disjoint Evaluation*. Indian Institute of Technology Kharagpur.

## License

A repository license has not yet been specified. Add a `LICENSE` file before granting others explicit permission to reuse or redistribute the code.
