# Solutions to the worksheet

## Solutions

1. Here is the code snippet. The solution should be a 3142 long peptide sequence
   a. Here is the code and the answer

   ```R
   rna <- readDNAStringSet("cDNA_sequence.fa")
   pep_res <- translate(rna)
   print(pep_res)

   pep_known <- readAAStringSet("solutions/peptide_sequence.fa")
   pwalign::pairwiseAlignment(pep_res, pep_known)
   ```

   ```python
   from Bio import SeqIO
   rna = SeqIO.read("cDNA_sequence.fa", "fasta")
   pep_res = a.translate()

   pep_known = SeqIO.read("peptide_sequence.fa", "fasta")
   ```

   b. c. Use BLAST and evaluate the result.

   ```text
   ENST000000355072.11
   HTT
   ENSG00000197386.15
   NCBI id: NM_00138492.1
   MANE Select
   ```

2. Check the GENCODE FAQ for answers.
   a. HAVANA means it is manually annoated.
   b,c. Coordinate system of GTF file is 1-based and Fully-closed. (Length = end-start+1)
   d. Level is whether it is verified, manually annotated or automatically annotated
   e. The coding strand of the gene

3. There should be 9 "Basic Gencode Transcripts" for the Gene SAMD11. They should have 3 unique Transcription start sites.

   ```bash
   rg ENSG00000187634 gencode.v50.basic.annotation.gtf | \
   awk '$3=="transcript"' | \
   cut -f 4 | \
   uniq

   > 923895
   > 923923
   > 925731
   ```

4. Types of genes present in GTF file (GENCODE basic annotation):

   ```R
     entries <- rtracklayer::import("~/Downloads/gencode.v50.basic.annotation.gtf")
     table(entries$gene_type)
   ```

5. Here is the Rscript:

   ```R
   entries <- rtracklayer::import("~/Downloads/gencode.v50.basic.annotation.gtf")
   prot <- entries[entries$gene_type == "protein_coding" & entries$level %in% c(1,2) & entries$type == "gene"]
   prot <- split(prot, f = strand(prot))
   overlaps <- GenomicRanges::findOverlaps(prot[["+"]], prot[["-"]], type = "any", ignore.strand = TRUE)
   overlaps@from |> unique() |> length()
   overlaps@to |> unique() |> length()
   overlaps |> length()
   ```

   ```text
   overlaps@from |> unique() |> length()
   3127
   overlaps@to |> unique() |> length()
   3134
   overlaps |> length()
   3823
   ```

6. Basic processing:

   ```R
   # a Exon length
   entries <- rtracklayer::import("~/Downloads/gencode.v50.basic.annotation.gtf")
   exons <- entries[entries$gene_type == "protein_coding" & entries$level %in% c(1,2) & entries$type == "exon"]
   unique_exons <- exons[!duplicated(exons$exon_id)]
   mean(width(unique_exons)) # 470 bp
   sd(width(unique_exons)) # 470 bp

   # b Intron lengths
   # split exons into per gene, but only use the largest transcript
   largest_transcript <- entries[entries$type == "transcript" & entries$gene_type == "protein_coding" & entries$level %in% c(1,2)] |>
       as.data.frame() |>
       dplyr::mutate(width = abs(end - start)) |>
       dplyr::slice_max(by = gene_id, order_by = width, n = 1, with_ties = FALSE) |>
       dplyr::pull(transcript_id)

   # Get all the exons for this transcript
   exons <- entries[entries$type == "exon" & entries$gene_type == "protein_coding" & entries$level %in% c(1,2)]
   exons <- exons[exons$transcript_id %in% largest_transcript]
   exons_l <- split(exons, f = exons$gene_id)
   compute_intron_length <- function(granges) {
       granges <- sort(granges)
       intron_lengths <- start(granges) - dplyr::lag(end(granges)) + 1
       return(intron_lengths)
   }
   intron_lengths <- lapply(FUN = compute_intron_length, exons_l) |>
       unlist()
   mean(intron_lengths, na.rm= TRUE) # 6693 bases
   sd(intron_lengths, na.rm= TRUE) # 196083

   # c
   # The gene with the largest intron is ENSG00000250349: 1240122 bp!
   ```

7. Alanine it is, with 34 high confidence tRNA gene set
