![](WCM_MB_LOGO_HZSS1L_CLR_RGB.png)

# Bioinformatic methods for Gramatica and Miller1 et al.

The quality of the sequenced reads was assessed with FastQC and QoRTs. Unless stated otherwise, plots involving high- throughput sequencing data were created using R and ggplot2.

## RNA-SEQ data analysis
Raw reads were quality checked with FastQC v0.11.7 (http://www.bioinformatics.babraham.ac.uk/projects/fastqc/). Reads were aligned to the human reference genome (GRCh38.p12) using STAR v2.6.0c with default parameters. Gene abundances were calculated with featureCounts v1.6.2 using composite gene models from Gencode release v28. Differentially expressed genes were determined with DESeq2 v1.34.0 using Wald tests (q < 0.05).

## Software used

| Software           | Version    | Authors           | URL                                                               |
|--------------------|------------|-------------------|-------------------------------------------------------------------|
| STAR               | v2.6.0c    | Dobin et al.      | [GitHub](https://github.com/alexdobin/STAR/releases)             |
| featureCounts     | v1.6.2      | Liao et al.       | [Subread](https://subread.sourceforge.net/featureCounts.html)    |
| R                  | v4.1.0      | R Core Team       | [CRAN](https://cran.r-project.org)                               |
| DESeq2             | v1.34.0     | Love et al.       | [Bioconductor](https://bioconductor.org/packages/release/bioc/html/DESeq2.html) |
| ggplot2            | v3.4.1      | Wickham           | [CRAN](https://cran.r-project.org/web/packages/ggplot2/index.html) |
| pheatmap           | v1.0.12     | Kolde             | [CRAN](https://cran.r-project.org/web/packages/pheatmap/index.html) |
