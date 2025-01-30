# Trim-Galore
### 26 SEP 2024

Trim-Galore is a wrapper for FastQC and Cutadapt, which will automatically detect the adapter sequences, trimming off the adapters and the low quality bases.

We use these flags for trim-galore:
  - `--paired` for paired-end reads
  - `--gzip` to compress the output files
  - `--length 75` to remove reads shorter than 50bp
  - `-j 8` to use 8 cores