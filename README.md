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
   if (!require(devtools)) install.packages("devtools")
   
   devtools::install_github("BUStools/BUSpaRse")
   BiocManager::install("BSgenome")
   
   # Load the packages
   library(BUSpaRse)
   library(Bsgenome)
   ```
   For macOS user, if you encounter any installation error, then pay ths [site](https://github.com/BUStools/BUSpaRse) a visit.
   
3. Prepare your genome file (.fasta) and annotation file (.gtf) :

   You will need to convert your fasta into a BSgenome object. In case the genome you want to use is already available as a BSgenome object, simply install it from Bioconductor and load it:
   ```
   # Install Arabidopsis TAIR9 genome
   BiocManager::install("BSgenome.Athaliana.TAIR.TAIR9")
   
   # Install Rice MSU7 genome
   BiocManager::install("BSgenome.Osativa.MSU.MSU7")
   
   # Load the genomes as BSgenome object
   library(BSgenome.Athaliana.TAIR.TAIR9)
   library(BSgenome.Osativa.MSU.MSU7)
   
   #Check the genomes
   > BSgenome.Athaliana.TAIR.TAIR9
   Arabidopsis genome:
   # organism: Arabidopsis thaliana (Arabidopsis)
   # provider: TAIR
   # provider version: TAIR9
   # release date: June 9, 2009
   # release name: TAIR9 Genome Release
   # 7 sequences:
   #   Chr1 Chr2 Chr3 Chr4 Chr5 ChrM ChrC                                                                 
   # (use 'seqnames()' to see all the sequence names, use the '$' or '[[' operator to access a given
   # sequence)
   
   > BSgenome.Osativa.MSU.MSU7
   Rice genome:
   # organism: Oryza sativa (Rice)
   # provider: MSU
   # provider version: MSU7
   # release date: October 31, 2011
   # release name: MSU7 Genome Release
   # 16 sequences:
   #   Chr1  Chr2  Chr3  Chr4  Chr5  Chr6  Chr7  Chr8  Chr9  Chr10 Chr11 Chr12 ChrM  ChrC  ChrUn ChrSy      
   # (use 'seqnames()' to see all the sequence names, use the '$' or '[[' operator to access a given
   # sequence)
   ```
