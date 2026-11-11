1. The file can be downloaded with:

   ```bash
   wget http://ftp.1000genomes.ebi.ac.uk/vol1/ftp/release/20130502/ALL.chr22.phase3_shapeit2_mvncall_integrated_v5b.20130502.genotypes.vcf.gz
   ```

   a. All the values in the line starting with ^#CHROM ....
   b. 2513
   c. 2504 = exactly matches what is listed on the webpage
   d. All these are in the header section
   e. QUAL = Quality score
   f. GT = Genotype - representated as Allele1|Allele2 or Allele1/Allele2 (when unphased)
   g. DP = Read depth - number of sequencing reads (DNA fragments) sequenced at at his location
   h. AF = Alternate allele frequency
   i. I obtained: chr22:16050213

2. Paste in the coordinate at the top bar in the IGV, then look for a cyan block by scrolling.

3. Install using mamba

   ```python
   from cyvcf2 import VCF
   import os
   import numpy as np
   vcf_file = "ALL.chr22.phase3_shapeit2_mvncall_integrated_v5b.20130502.genotypes.vcf.gz"

   for variant in VCF(vcf_file):
     afr_af = variant.INFO.get("AFR_AF")
     af = variant.INFO.get("AF")
     if (isinstance(afr_af, tuple)):
       afr_af = max(afr_af)
     if (isinstance(af, tuple)):
       af = max(af)
     if(afr_af>0.1 and af < 0.1):
       break

   ```

4. In the region chr22:37,457,576-37,457,671 there should be 5 variants. Four of them should overlap the exons of the gene KCTD17.

   ```python
   from cyvcf2 import VCF, Writer
   import os
   import numpy as np
   vcf = VCF("ALL.chr22.phase3_shapeit2_mvncall_integrated_v5b.20130502.genotypes.vcf.gz")
   w = Writer("out.vcf", vcf)

   for variant in vcf("22:37,457,576-37,457,671"):
       w.write_record(variant)
   w.close()

   ```

   a. NCBI RefSeq annotation shows 4 KCTD17 transcripts. Of which three have exons in this region.
   b. Go to VEP and submit this VCF file for annotation. Make sure to chose the "HGVS" annotation
   c. The HGVS nomenclature of the variant at chr22:37457585 is NM_001282684.2:c.791G>A

5. The sequences of these transcripts can be obtained at the [ENSEMBL](https://www.ensembl.org/feature-explorer/GCA_000001405.29/transcript:ENST00000335295.4).

   ```fasta
   >ENST00000335295.4 cds
   ATGGTGCATCTGACTCCTGAGGAGAAGTCTGCCGTTACTGCCCTGTGGGGCAAGGTGAAC
   GTGGATGAAGTTGGTGGTGAGGCCCTGGGCAGGCTGCTGGTGGTCTACCCTTGGACCCAG
   AGGTTCTTTGAGTCCTTTGGGGATCTGTCCACTCCTGATGCTGTTATGGGCAACCCTAAG
   GTGAAGGCTCATGGCAAGAAAGTGCTCGGTGCCTTTAGTGATGGCCTGGCTCACCTGGAC
   AACCTCAAGGGCACCTTTGCCACACTGAGTGAGCTGCACTGTGACAAGCTGCACGTGGAT
   CCTGAGAACTTCAGGCTCCTGGGCAACGTGCTGGTCTGTGTGCTGGCCCATCACTTTGGC
   AAAGAATTCACCCCACCAGTGCAGGCTGCCTATCAGAAAGTGGTGGCTGGTGTGGCTAAT
   GCCCTGGCCCACAAGTATCACTAA
   ```

   Implement these variants using R or python

   ```R
   library(Biostrings)
   hbb <- readDNAStringSet("ENST00000335295.4_hbb_cds.fasta")
   translate(hbb) # to get the protein sequence
   # Variant 1
   translate(replaceAt(hbb, at = IRanges(start = 397, end = 397), value = "T")) # Gain of an early stop codon # NCBI clinvar search shows non-sense.
   # Variant 2
   translate(replaceAt(hbb, at = IRanges(start = 343, end = 344), value = "G")) # Shows a frame shift. Also pathogenic as per Clinvar
   # Variant 3
   translate(replaceAt(hbb, at = IRanges(start = 373, end = 373), value = "A")) # Shows a frame shift. Benign missense variant as per Clinvar
   ```

6. Just go to [clinvar](https://www.ncbi.nlm.nih.gov/clinvar/), search HBB in the "gene" textbox and select the required filter.

7. One can get the spliced sequence of the gene and the coding region from ENSEMBL. Search for the gene, choose the transcript, and click on download. Here, you can choose what sequence of the transcript you want to download. [GJB2_transcript](https://www.ensembl.org/feature-explorer/GCA_000001405.29/transcript:ENST00000382848). Since the HGVS nomenclature of the c.36delG corresponds to coding sequence, use that.

   ```fasta
   >ENST00000382848.5 cds
   ATGGATTGGGGCACGCTGCAGACGATCCTGGGGGxTGTGAACAAACACTCCACCAGCATT
   GGAAAGATCTGGCTCACCGTCCTCTTCATTTTTCGCATTATGATCCTCGTTGTGGCTGCA
   AAGGAGGTGTGGGGAGATGAGCAGGCCGACTTTGTCTGCAACACCCTGCAGCCAGGCTGC
   AAGAACGTGTGCTACGATCACTACTTCCCCATCTCCCACATCCGGCTATGGGCCCTGCAG
   CTGATCTTCGTGTCCACGCCAGCGCTCCTAGTGGCCATGCACGTGGCCTACCGGAGACAT
   GAGAAGAAGAGGAAGTTCATCAAGGGGGAGATAAAGAGTGAATTTAAGGACATCGAGGAG
   ATCAAAACCCAGAAGGTCCGCATCGAAGGCTCCCTGTGGTGGACCTACACAAGCAGCATC
   TTCTTCCGGGTCATCTTCGAAGCCGCCTTCATGTACGTCTTCTATGTCATGTACGACGGC
   TTCTCCATGCAGCGGCTGGTGAAGTGCAACGCCTGGCCTTGTCCCAACACTGTGGACTGC
   TTTGTGTCCCGGCCCACGGAGAAGACTGTCTTCACAGTGTTCATGATTGCAGTGTCTGGA
   ATTTGCATCCTGCTGAATGTCACTGAATTGTGTTATTTGCTAATTAGATATTGTTCTGGG
   AAGTCAAAAAAGCCAGTTTAA
   ```

   Now delete the 35th base (G) and use any of the translation exercise we did earlier.

   The notation of ENST00000382848.5:c.35delG is not allowed per HGVS, because the deleted base must not be stated.

8. SIFT accepts only a protein sequence. So, one needs to download cDNA, apply the variant on it, translate it and then work with it. Polyphen2 access protein IDs. You can get the protein ID ("NP_xxx") based on the transcript id ("NM_xxxx") from the NCBI database.
   a,b. WT HBB protein sequence:

   ```fasta
    >NP_000509.1 hemoglobin subunit beta [Homo sapiens]
    MVHLTPEEKSAVTALWGKVNVDEVGGEALGRLLVVYPWTQRFFESFGDLSTPDAVMGNPK
    VKAHGKKVLGAFSDGLAHLDNLKGTFATLSELHCDKLHVDPENFRLLGNVLVCVLAHHFG
    KEFTPPVQAAYQKVVAGVANALAHKYH
   ```

   c. WT PAX3 protein

   ```fasta
       >NP_852123.1 paired box protein Pax-3 isoform PAX3d [Homo sapiens]
   MTTLAGAVPRMMRPGPGQNYPRSGFPLEVSTPLGQGRVNQLGGVFINGRPLPNHIRHKIVEMAHHGIRPCVISRQLRVSHGCVSKILCRYQETGSIRPGAIGGSKPKQVTTPDVEKKIEEYKRENPGMFSWEIRDKLLKDAVCDRNTVPSVSSISRILRSKFGKGEEEEADLERKEAEESEKKAKHSIDGILSERASAPQSDEGSDIDSEPDLPLKRKQRRSRTTFTAEQLEELERAFERTHYPDIYTREELAQRAKLTEARVQVWFSNRRARWRKQAGANQLMAFNHLIPGGFPPTAMPTLPTYQLSETSYQPTSIPQAVSDPSSTVHRPQPLPPSTVHQSTIPSNPDSSSAYCLPSTRHGFSSYTDSFVPPSGPSNPMNPTIGNGLSPQVMGLLTNHGGVPHQPQTDYALSPLTGGLEPTTTVSASCSQRLDHMKSLDSLPTSQSYCPPTYSTTGYSMDPVTGYQYGQYGQSAFHYLKPDIA
   ```

   This is the variant sequence

   ```fasta
   >NP_852123.1 paired box protein Pax-3 isoform PAX3d [Homo sapiens]
   MTTLAGAVPRMMRPGPGQNYPRSGFPLEVSTPLGQGRVNQLGGVFINGRPLPNHIRHKIVEMAHHGIRPCVISRQLRVSHGCVSKILCRYQETGSIRPGAIGGSKPKQVTTPDVEKKIEEYKRENPGMFSWEIRDKLLKDAVCDRNTVPSVSSISRILRSKFGKGEEEEADLERKEAEESEKKAKHSIDGILSERASAPQSDEGSDIDSEPDLPLKRKQRRSRTTFTAEQLEELERAFERTHYPDIYTREELAQRAKLTEARVQVWFSNRRARWRKQAGANQLMAFNHLIPGGFPPTAMPTLPTYQLSETSYQPKSIPQAVSDPSSTVHRPQPLPPSTVHQSTIPSNPDSSSAYCLPSTRHGFSSYTDSFVPPSGPSNPMNPTIGNGLSPQVMGLLTNHGGVPHQPQTDYALSPLTGGLEPTTTVSASCSQRLDHMKSLDSLPTSQSYCPPTYSTTGYSMDPVTGYQYGQYGQSAFHYLKPDIA
   ```

   Polyphen2 shows that the PAX3d variant is possibly damaging

9. SpliceAI values shows the effect on the splicing donors and acceptors on a scale of 0 to 1.
