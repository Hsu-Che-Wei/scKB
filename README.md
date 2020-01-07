# kallisto-bustools
Use kallisto and bustools to call the gene-cell matrix for both spliced and unspliced transcripts of scRNA-seq

## Preparation

1. Install "kallisto" and "bustools" in your Unix/Linux environment :
   
   The easiest way is through "conda". If you are new to conda, follow this nice [tutorial](https://www.digitalocean.com/community/tutorials/how-to-install-anaconda-on-ubuntu-18-04-quickstart).
   
   ```
   conda install -c bioconda bustools
   conda install -c bioconda kallisto
   ```

2. Install package "BUSpaRse" and "BSgenome" in your R environment :
   
   The two packages are hosted in "Bioconductor", start your R session and type:
   
   ```
   if (!requireNamespace("BiocManager", quietly = TRUE))
    install.packages("BiocManager")
   
   BiocManager::install("BUSpaRse")
   BiocManager::install("BSgenome")
   ```
