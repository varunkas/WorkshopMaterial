# Worksheet

## Variant Formats

1. Download the VCF file from the Phase 3 of the 1000 Genome project from [here](https://www.internationalgenome.org/data-portal/population). Download the VCF file corresponding to one of the smaller autosomes (say chr22). Also look at other information at the webpage for other information about the dataset - e.g., the population represented in this dataset.
   a. What are the columns in the data section of the VCF file?
   b. How many columns are the in the data section VCF file?
   c. How many samples does this VCF store variants of?
   d. Figure out the all the values written in the FORMAT and INFO fields. What do the following abbreviations stand for?
   e. QUAL
   f. GT
   g. DP
   h. AF
   i. Find the first Homozygous ALT loci. At which chromosomal location is it in?

2. Open the same VCF file in Integrative Genome Viewer with the human genome sequence GRCh38. Go to the homozougous location that was identified. Find another variant in a gene body. Discuss with your partner as to what could this cause in terms of the gene function?

3. Install cyVCF2 for python or vcfR for R and filter out the following variants:
   a. Get the name of all samples
   b. Find a variant with alternate allele frequency in african population (AFR) > 0.1 and the general alternate allele frequency <0.1.

4. Locate the variants in the region chr22:37,457,576-37,457,671 in the above downloaded VCF file. Use cyVCF2 to subset only variants in the regions to a file. Using one of the annotation tools (SnpEff, AnnoVar, **VEP**) and figure out the following. This might take some time:
   a. Which transcripts are affected
   b. Is it a LOF variant. Is it pathogenic? Answer this by using the above annotation tools.
   c. Note the HGVS nomenclature. Note for change in notation when the variant is in the intron

5. Decoding HGVS nomenclature: For the following variants in the HGVS nomenclature
   a. Obtain the corresponding reference sequences (use CDS)
   b. Figure out the variant sequence
   c. Guess the consequence of these variants using tranlation with R or python
   d. Search Clinvar to see what the annotated consequence is. When doing this, use the RefSeq transcript ID: NM_000518.5

   ```text
   Variant1: ENST00000335295.4(HBB):c.397A>T
   Variant2: ENST00000335295.4(HBB):c.343_344delinsG
   Variant3: ENST00000335295.4(HBB):c.373C>A
   ```

   In this exercise, lets go to

   Lets do the same from https://www.ncbi.nlm.nih.gov/clinvar/

6. Use Clinvar to figure out how many different variants in the HBB gene are associated with beta-thalassemia as pathogenic or likely pathogenic. Explore the distribution of:
   a. Molecular consequences (missense, frameshift, etc.)
   b. Variant types (Substitution, structural variant etc.)

7. Check the effect of the variant GJB2 gene (get the ncbi id) ENST00000382848.5:c.35delG when the gene product is translated. Download from ENSEMBL the sequence and translate it.

8. Check the predicted effect of the following variants using [SIFT](https://siftdna.org/www/SIFT_seq_submit2.html) or [Polyphen-2](https://genetics.bwh.harvard.edu/pph2/index.shtml) or [MutationTaster](https://teide.charite.de/MutationTaster2021/).
   a. `HBB NM_000518.4:c.244C>G p.Leu82Val`
   b. `HBB NM_00518.4:c.190C>A p.His64Asn`
   c. `NP_852123:p.315T>K`

   Discuss how SIFT works, based on the the details provided at [SIFT Help Page](https://siftdna.org/www/SIFT_help.html#SIFT_PROCEDURE)

9. Check the effect of splice variants using [SpliceAI](https://spliceailookup.broadinstitute.org/)
   a. NM_000518.4:c.92+1G>A
   b. Find a pathogenic splice variant in your favourite gene from ClinVar and see what SpliceAI has to say about it.

10. From your perusal at home - GWAS hands-on tutorials.
    a. https://gwas-intro-bajicv.readthedocs.io/en/latest/
    b. https://github.com/MareesAT/GWA_tutorial/
    c. https://www.mv.helsinki.fi/home/mjxpirin/GWAS_course/
