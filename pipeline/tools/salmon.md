# Salmon
### 26 SEP 2024

Salmon is a tool for quantifying the expression of transcripts using RNA-seq data. It runs on a special "index" file which allows it to quickly map reads onto the transcriptome.

To create the Salmon index file (which we dub *.sidx) we run
```
salmon index \
    -t transcripts.fasta \    # the transcriptome file as a fasta
    -i transcripts.sidx \     # the output index directory
    -p 8                      # the number of cores to use
```

To quantify samples with Salmon we run
```
salmon quant --libType A \                  # the library type (A for automatic)
    -1 read1.fq.gz -2 read2.fq.gz \         # the input reads
    -i !{reference}/*short_index*.sidx \    # the index file
    --output sample.quant \                 # the output directory
    --seqBias \                             # correct for sequence bias
    --gcBias \                              # correct for GC bias
    --validateMappings \                    # validate the mapping locations
    --recoverOrphans \                      # recover orphaned reads (one mate doesn't map)
    --rangeFactorizationBins 4              # the number of range factorization bins
    --writeUnmappedNames \                  # write the names of unmapped reads
    -p 8                                    # use 8 cores to quant
```

Salmon outputs a quantification folder with a lot of files, the most important of which is titled `quant.sf`. This file contains the estimated counts and TPM values for each transcript in the transcriptome as a tab-separated file.

There is also a directory called `libParams` which contains the parameters used to quantify the samples. This is useful for reproducibility and debugging.