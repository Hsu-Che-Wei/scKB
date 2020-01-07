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
   
   The two packages are hosted in "Bioconductor", start your R session and simply type:
   
   ```
   if (!requireNamespace("BiocManager", quietly = TRUE))
    install.packages("BiocManager")
   
   BiocManager::install("BUSpaRse")
   BiocManager::install("BSgenome")
   
   # Load the packages
   library(BUSpaRse)
   libaray(Bsgenome)
   ```
3. Prepare your genome file (.fasta) and annotation file (.gtf) :

   You will need to convert your fasta into a BSgenome object. In case the genome you want to use is already available as a BSgenome object, simply install it from Bioconductor and load it:
   ```
   # Install Arabidopsis TAIR9 genome
   BiocManager::install("BSgenome.Athaliana.TAIR.TAIR9")
   
   # Install Rice MSU7 genome
   BiocManager::install("BSgenome.Osativa.MSU.MSU7")
   
   # Load the genome as BSgenome object
   library(BSgenome.Athaliana.TAIR.TAIR9)
   libaray(BSgenome.Osativa.MSU.MSU7)
   ```
