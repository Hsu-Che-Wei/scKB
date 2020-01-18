# scKB
Use kallisto and bustools to call the gene-cell matrix for both spliced and unspliced transcripts of scRNA-seq

## Preparation

**Make sure that you have downloaded the scKB script, whitelists (unzipped it once downloaded) and scKB.yml files.**

1. Install "kallisto" and "bustools" in your Unix/Linux environment :
   
   The easiest way is through "conda". If you are new to conda, follow this nice [tutorial](https://www.digitalocean.com/community/tutorials/how-to-install-anaconda-on-ubuntu-18-04-quickstart). 
   ```
   conda install -c bioconda bustools
   conda install -c bioconda kallisto
   ```
   It is recommended that you create a new conda environment for only the use of scKB :
   ```
   # create the environment named scKB, which will have kallisto, bustools and R (version 3.6.1) installed
   conda env create -f scKB.yml
   
   # activate the environment
   conda activate scKB
   
   # start the R session
   R
   ```
2. Install package "BUSpaRse" and "BSgenome" in your R environment (tested R version = 3.6.1) :
   
   The two packages are hosted in "Bioconductor", make sure your are in R and type :
   
   ```
   if (!requireNamespace("BiocManager", quietly = TRUE))
    install.packages("BiocManager")
   if (!require(devtools)) install.packages("devtools")
   
   devtools::install_github("BUStools/BUSpaRse")
   BiocManager::install("BSgenome")
   ```
   For macOS user, if you encounter any installation error for BUSpaRse, then pay ths [site](https://github.com/BUStools/BUSpaRse) a visit.
   
3. Prepare your genome file (.fasta) and annotation file (.gtf) :

   You will need to convert your fasta into a BSgenome object. In case the genome you want to use is already available as a BSgenome object, simply install it from Bioconductor and load it :
   
   In R :
   
   ```
   # Install Arabidopsis TAIR9 genome
   BiocManager::install("BSgenome.Athaliana.TAIR.TAIR9")
   
   # Install Rice MSU7 genome
   BiocManager::install("BSgenome.Osativa.MSU.MSU7")
   
   # Load the genomes as BSgenome object
   library(BSgenome.Athaliana.TAIR.TAIR9)
   library(BSgenome.Osativa.MSU.MSU7)
   
   # Check the genomes
   BSgenome.Athaliana.TAIR.TAIR9
   
   # Arabidopsis genome:
   # organism: Arabidopsis thaliana (Arabidopsis)
   # provider: TAIR
   # provider version: TAIR9
   # release date: June 9, 2009
   # release name: TAIR9 Genome Release
   # 7 sequences:
   #   Chr1 Chr2 Chr3 Chr4 Chr5 ChrM ChrC                                                                 
   # (use 'seqnames()' to see all the sequence names, use the '$' or '[[' operator to access a given
   # sequence)
   
   BSgenome.Osativa.MSU.MSU7
   
   # Rice genome:
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
   **Make sure that the name of chromosomes/sequence is exactly the same in annotation file (.gtf) and the BSgenome object!** 

   If the genome is not available as BSgenome object or you have added new sequences manually to the genome in fasta format, then you will need to convert the fasta file into BSgenome object following [this manual](https://bioconductor.org/packages/release/bioc/vignettes/BSgenome/inst/doc/BSgenomeForge.pdf).
   
4. Extract the intron information and make kallisto index
   
   Here a example of Arabidopsis single cell data prepared using "10X v3 chemistry" is provided.
   
   In R :
   ```
   # Load the package and genome
   library(BUSpaRse)
   library(BSgenome.Athaliana.TAIR.TAIR9)
   
   # X = directory to your annotation file, L set to 91 if you are using 10X v3 chemistry, 98 if you are using 10X v2 chemistry
   get_velocity_files(X = "/dir/to/gtf/file/Arabidopsis_thaliana.TAIR10.43.gtf", L = 91, Genome = BSgenome.Athaliana.TAIR.TAIR9, out_path = "/dir/to/output/intron/file", isoform_action = "separate")
   
   # Index the intron file with kallisto
   system("kallisto index -i /dir/to/output/index/file/cDNA_introns_10xv3.idx /dir/to/output/intron/file/cDNA_introns.fa")
   ```
5. Download the whitelist file and unzipped it

## scKB

 In Unix/Linux environment, make sure that both "kallisto" and "bustools" can be found and executed.
 
 Usage :
 
 ```
scKB [-h] | [-f fastq_dir -i intron_index_dir -d intermediate_files_ouput_dir -s single_cell_technique_supported -t number_of_threads_used -w whitelist_dir -n results_output_dir]

-h (--help) = software usage
-f (--file) = directory to fastq files
-i (--index) = directory to intron index files (includes the .idx file itself)
-d (--dir_intermediate) = directory to intermediate files output
-s (--supported_tech) = single cell technique supported

short name       description
----------       -----------
10xv1            10x version 1 chemistry
10xv2            10x version 2 chemistry
10xv3            10x version 3 chemistry
CELSeq           CEL-Seq
CELSeq2          CEL-Seq version 2
DropSeq          DropSeq
inDrops          inDrops
SCRBSeq          SCRB-Seq
SureCell         SureCell for ddSEQ

-t (--thread) = number of threads used
-w (--whitelist) = directory to whitelist file (includes the text file itself)
-n (--dir_final) = directory to final output that includes gene cell matrix
 ```
Example (in Linux/Unix under the directory where scKB script is located):

```
./scKB -f /dir/to/fastq/files -i /dir/to/output/index/file/cDNA_introns_10xv3.idx -d /dir/to/output/intermediate/files -s 10xv3 -t 8 -w dir/to/whitelist/file/10xv3_whitelist.txt -n /dir/to/output/gene/cell/matrix
```

**Under the directory that you submit to -f, make sure that you have sequences of at least one run (I1, R1 and R2 gzipped fastq). Multiple runs of the same sample should be put under the same directory. Each run of scKB will call the gene-cell matrix for one sample.**

**Only example for 10X version 3 chemistry is provided here, and only the whitelist files for 10X version 2 and version 3 are provided.**




