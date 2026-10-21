# Foundations in medicine - Worksheet

My solutions are mainly based on R. May questions can be solved using awk or other Linux utilities. Python might also have suitable libraries.

## Set up

You will need to install several programs to run these. Suggest having a [conda/mamba](https://mamba.readthedocs.io/en/latest/installation/mamba-installation.html) environment set up, so you can install everything there.

If using R, consider installing:

- [Biostrings](https://bioconductor.org/packages//release/bioc/html/Biostrings.html) - for manipulating sequences
- [rtracklayer](https://bioconductor.org/packages//release/bioc/html/rtracklayer.html) - to load GTF files

If using python, consider installing:

- [Biopython](https://biopython.org/wiki/SeqIO)

## Links to useful sources of genomic data/visualisations for this workshop

- [UCSC Genome Browser](https://genome-euro.ucsc.edu) - for visualising genomic regions. Table browser for downloading data.
- [GENCODE](https://www.gencodegenes.org/human/) - to obtain gene annotations.
- [BLAST](https://blast.ncbi.nlm.nih.gov/Blast.cgi) (Basic Local Alignment Search Tool) - for friendly alignment of sequences, against each other or against sequences in public databases

## Exercises

1. For the [cDNA sequence](cDNA_sequence.fa) do the following:
   a. Create a script in R or Python to translate the it to its a protein Sequence (translation). Use packages
   b. Figure out which Gene this sequence could have come from. Tip - you can use BLAST. Provide the ENSEMBL ID of the gene and transcript. ENSEMBL ids start with ENS....

2. Familiarise yourselves with [GTF format](https://www.gencodegenes.org/pages/data_format.html) used to store gene-like annotations. What do the following mean:
   a. "HAVANA" in column 2
   b. Figure out the co-ordinate system: Is it 0-based or 1-based?
   c. Figure out the co-ordinate system: Half-Open or Half Closed?
   d. What does level stand for?
   e. What are the "+" and "-" symbols in column 7 mean?

3. How many alternate Transcription Start Sites does the gene "SAMD11 (ENSG00000187634)" have? Use the [GENCODE GTF basic annotation file](https://ftp.ebi.ac.uk/pub/databases/gencode/Gencode_human/release_50/gencode.v50.basic.annotation.gtf.gz) to figure out. Verify by eye using the UCSC Genome Browser.

4. What are the various gene types are present in the [GTF file](https://ftp.ebi.ac.uk/pub/databases/gencode/Gencode_human/release_50/gencode.v50.basic.annotation.gtf.gz)? Look up what the gene types that were not covered in the lecture.

5. How often do protein-coding genes in the positive and negative strands overlap in the human genome. Use only verified and manually annotated genes? What does this mean?

6. Calculate the statistics of the following elements in the split-gene structure in the human geneome (of protein-coding genes with levels 1 or 2) using the GTF file:
   a. Mean and sd of the length of Exons
   b. Mean and sd of the length of Introns across all genes
   c. Find the gene with the largest intron. What kind of a gene is it?

7. Which aminoacid which has the smallest and largest number of tRNA. Use the database (https://gtrnadb.ucsc.edu/genomes/eukaryota/Hsapi19/)

## Extra questions

1. What class/family/type of repeat sequence are the most common in the genome. How many repeats have been annotated in the Human Genome of the name "MLT1K". Tip: Use the table browser in UCSC genome browser to download the repeat masker table in the format of a BED file.

2. Figure out how the repeat elements are distributed across the genome. Are they uniformly distributed across the genome? Tip: Use the above file.

3. Find out the repeat unit in the following sequence:

    ```text
    TCCCCATCCTCCTAGGAGGTCACTGGGGAGACAGGGGGCAGGGTCAGCCC
    AGTGCCAAAGGATGGGCAGGATCTGAAGTGTGGAAATGGAGTAAGGCTGT
    GTCTGTGTCAGAGGTGGGTTGGGAAGATGTGAGACAAACATCACAATTTT
    GCCTAAGGTGAATCCAACCCACAAGTAGAGCACAGGCCAACAGCAGCTCA
    CTAGTACACATACTTACACCAGCAGCTCACTAGTACACACACTTACACCA
    GACGCTCACTGGTACACACTCACACCAGAAGCTCATTAGAACACACACAC
    CGTCAGCTTGCTAGTACACACACTTACACCAGAAGCTCACTAGTATACAC
    TTACACCAGAAGCTCACTAGTACACACACTTACTCCAGAAGCTCACTAGT
    ACACACACTTACACCCACAGAGACAAGCCCCACACCACACGGACTCACAA
    ATGCAGAAGAAGAGTTGACCCTGCCCTCATGTACAGATAGTATGCTGGTG
    ```

    a. Collect ideas on how to look for signs of repeated sequence?
    b. Develop an algorithm

4. Pick two of your favourite Human Genes, find their mouse Orthologs and figure out where they are located in the mouse genome. Use [ENSEMBL BioMart](https://mart.ensembl.org/) to download mouse orthologs for human genes. Predownloaded data in `Downloaded_Data/ENSEMBL_BioMart_HumanMouseOrthologs.txt`.
   a. How many genes have no orthologs
   b. How many have exactly 1 ortholog
   c. How many have >1 ortholog (What does this mean?)

5. Familiarise yourselves with the NCBI comparative genomics viewer. Look for alignment between Human (GRCh38) and Mouse (mm38).
   a. Find a segment that is Syntenic.
   b. Find a segment that is not Syntenic.
