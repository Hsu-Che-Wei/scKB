# scKB
Use kallisto and bustools to call the gene-by-cell matrix for both spliced and unspliced transcripts of scRNA-seq

## Download 

In Linux:

```
git clone https://github.com/Hsu-Che-Wei/scKB.git

cd ./scKB

# unzip whitelist files and gene annotation file
unzip 10xv2_whitelist.txt.zip
unzip 10xv3_whitelist.txt.zip
gunzip Arabidopsis_thaliana.TAIR10.43.gtf.gz

# make scKB executable
chmod 777 scKB
```

## Preparation

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
   For macOS user, if you encounter any error when installing BUSpaRse, then pay this [site](https://github.com/BUStools/BUSpaRse) a visit.
   
3. Prepare your genome file (.fasta) and annotation file (.gtf) :

   You will need to convert your fasta into a BSgenome object. In case the genome you want to use is already available as a BSgenome object, simply install it from Bioconductor and load it :
   
   In R :
   
   ```
   # Install Arabidopsis TAIR genome
   BiocManager::install("BSgenome.Athaliana.TAIR.TAIR9")
   
   # Install Rice MSU genome
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

   If the genome is not available as BSgenome object or you have added new sequences manually to the genome in fasta format, then you will need to convert the fasta file into BSgenome object following [this manual](https://bioconductor.org/packages/release/bioc/vignettes/BSgenome/inst/doc/BSgenomeForge.pdf) (check out section 2.2.5 and 2.3).
   
4. Extract the intron information and make kallisto index
   
   An example showing how to extract intron information and prepare kallisto index for _Arabidopsis_ singel cell sample prepared using "10X v3 chemistry" is provided here.
   
   In R :
   ```
   setwd("~/to/where/you/clone/the/repo/scKB")
   
   # Load the package and genome
   library(BUSpaRse)
   library(BSgenome.Athaliana.TAIR.TAIR9)
   
   # X = directory to your annotation file, L set to 91 if you are using 10X v3 chemistry, 98 if you are using 10X v2 chemistry
   get_velocity_files(X = "./Arabidopsis_thaliana.TAIR10.43.gtf", L = 91, Genome = BSgenome.Athaliana.TAIR.TAIR9, out_path = "./", isoform_action = "separate", chrs_only=FALSE, style="Ensembl")
   
   # After running get_velocity_files, the working directory should have files "cDNA_introns.fa", "cDNA_tx_to_capture.txt", "introns_tx_to_capture.txt" and "tr2g.tsv" 
   
   # Index the intron file with kallisto
   system("kallisto index -i ./cDNA_introns_10xv3.idx ./cDNA_introns.fa")
   
   quit()
   ```

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
In Linux:

```
cd ~/to/where/you/clone/the/repo/scKB

# run scKB, it is recommended to name the output directory as sample name using "-n" flag if one expect to use COPILOT with default parameters for downstream preprocessing 
./scKB -f ./toy_data -i ./cDNA_introns_10xv3.idx -d ./ -s 10xv3 -t 8 -w ./10xv3_whitelist.txt -n ./col0_toy

# After running scKB, the output directory (in this case "./col_toy") should have 8 files: "inspect.json", "run_info.json", "spliced.barcodes.txt", "spliced.genes.txt", "spliced.mtx", "unspliced.barcodes.txt", "unspliced.genes.txt" and "unspliced.mtx"
# json files have the reads/aligning stats of the sample, mtx files are gene-by-cell matrices, txt files have cell barcodes and gene ids information
```

**Caution!**

**1. Under the directory that you submit to -f, make sure that you have sequences of at least one run (I1 (optional), R1 and R2 gzipped fastq). Multiple runs of the same sample should be put under the same directory. Each run of scKB will call the gene-by-cell matrix for one sample.**

**2. The directory to intron file (see Preparation step 4), index file (-i), whitelist (-w) and intermediate files (-d) can be the same one, yet the final output directory (-n) should be different.**

**3. Only demonstration of processing 10X version 3 chemistry data is provided here, and only the whitelist files for 10X version 2 and version 3 are provided, yet, the techniques listed in usage are also supported.**
