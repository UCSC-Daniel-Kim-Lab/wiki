# Daniel Kim Lab Pipeline
## Alex D. Hill
### 24 SEP 2024

#### Intra and Extracellular RNA Isolation [TODO]

All of our experiments revolve around the study of RNA transcripts, meaning that for any of our work to produce usable data we must capture the RNA for analysis. We do this using RNA isolation methods developed in 'kits' which contain the reagents and protocols we need for extracting the RNA from a sample, whether that be cells or blood.

Intracellular kit

ExoRNEasy

nRichDx

#### Complementary DNA synthesis

RNA by itself is very unstable and incredibly difficult to work with. For this reason, we typically convert all of our RNA into comlementary DNA, or cDNA. We do this through a process called reverse-transcription, where the RNA transcripts are used as a template to write a new DNA strand. This process is repeated to both amplify the number of molecules in a sample (which is important especially when we are working with low-input samples) and to make sure we end up with double-stranded DNA molecules.

#### RNA Sequencing

RNA-sequencing is the process by which we analyze a cDNA library like described above. We attach special adapter sequences to the cDNA molecules through a process called ligation, which then allows the molecules to attach to flow cells in different sequencing machines. These machines try to read the bases in each molecule and return to us the sequence information.

##### Illumina short-reads [TODO]

Illumina sequencers use a process called bridge-amplification in which the adapter sequence on a molecule is used to attach the molecule to the base of a flow cell on both ends so that it is curved like an arch mounted to the flow cell. The molecule then has a complementary strand added to the DNA molecule.

##### Nanopore long-reads

Nanopore sequencing is less complex. Tiny pores in a membrane allow for a single molecule of DNA to pass through. As the molecule is walked through the pore by a special protein, the voltage across the pore is measured. Because each base has a unique chemical composition, they affect the voltage in the pore differently. The changes in the voltage are then used to infer what base is passing through the pore at what time. This way, entire molecules can be read at once without fragmentation, and the use of voltage can allow for the detection of non-canonical and modified bases that are otherwise not detectible (easily) with other methods of sequencing. 

#### Sequencing Data
Our data is all RNA sequencing data, the majority of which is from cDNA libraries. Sequencing data is stored in the FAST*A* or FAST*Q* format.

FASTA files (some_sample.fasta) holds plain sequences, stored as a 'header' line which tells you the assigned name of a sequence, followed by the sequence itself. The sequence is stored in any number of lines, each containing a fixed number of bases per line (usually 80). These two sections repeat for every sequence stored in a file.  

```
>chr1
CGATACAGTGATTTTGAAAGGTTTGCGGGGCACAGCTATGACTTGCTTAGCTACGTGTGAGGGAAGGAACTTTTGCGTATTTGTATGTTCACCCGTCTAC
TACGCATGCGGGCAGATTATGTAGGTTGAGAGATGCGGGAGAAGTTCTCGACCTTCCCGTGGGACGTGAACCTATCCCCTAATAGAGCATTCCGTTCGAG
CATGGCAGTAAGTACGCCTTCTCAATTGTGCTAACCTTCATCCCTATCAAAGCTTGGAGCCAATGATCAGGGTTATTCCCTTGGGACAGACTTCCTACTC
ACAGTCGGTCACATTGGGCTACTCCATGGGTCTTCAGCTTGACCCGGTCTGTTGGGCCGCGATTACGTGAGTTAGGGCCCCGGACTGCGCTGTATAGTCG
ATTCTCATCCGGCCCCCACATCTGGAAACCCCAACTTATTTAGATAACATGATTAGCCGAAGTTGCACGGCGTGTCCACC...
```

FASTQ files have a repeating 4 line structure instead of 2. They also contain sequence *Q*uality information stored as ASCII characters. The quality score is a measure of the confidence of the base call at that position. The higher the quality score, the higher the confidence in the base. The quality score is stored as an ASCII character, which is then converted to a numerical value by subtracting 33 from the ASCII value. The quality score is the same length as the sequence and is stored on a separate line. The lines in a FASTQ file are 1) the header, 2) the sequence, 3) a '+' character, and 4) the quality score.

Valid quality score characters are: `!"#$%&'()*+,-./0123456789:;<=>?@ABCDEFGHI`  

They have values of 0-40 respectively.

```
>read_1
CCATGTCGATACTGAACGTATCGACGCATACCTCCTTCCTTGAAAACGCACAATCATACAACTGGGCACATAATGCGTAC
+
!"#$%&'()*+,-./0123456789:;<=>?@ABCDEFGHI3456789:;<=>?@ABC3456789:;<=>?@ABC34567
```

#### Sequence Trimming

One of the first steps in any form of sequence analysis is trimming reads. We trim reads for two reasons: 1) to remove low quality bases which tend to appear at the end of reads and 2) to remove adapter sequences. Adapter sequences are short DNA sequences that are ligated to the ends of DNA fragments, which facilitate the attachment of the DNA fragment to the sequencing flow cell.

We currently use `Trim-Galore` as our trimmer, this is a wrapper program which uses Trimmomatic to automatically detect and remove adapters, remove low-confidense bases, and create some QC information with `FastQC`.

#### Sequence Alignment

From our RNA data we want to know what genes are producing the sequences that we have in our data. To do this we *align* our sequences onto the human genome.

We do this with a program called `Salmon` (Salmon actually does a process called pseudo-alignment which is a very fast way to determine where a sequence comes from without actually having to do a full genome alignment). Using Salmon we actually align each our our reads to the **transcriptome** instead of the genome, meaning that the best alignment Salmon can find is going to be the transcript that the read came from. Salmon is also special in that it produces a counts matrix, which is a table that has every individual transcript from our annotations as rows and every sample as a column. The values in the table are the number of reads that came from each transcript in each sample.

Most aligners return a SAM (sequence alignment map) or BAM (binary alignment map) file as output. These files contain the exact same information (BAMs are smaller than SAMs, but cannot be read without decoding). These files contain the coordinates that each aligned read maps to in the genome, as well as the quality of the alignment. We use this along with a counting program like `featureCounts` or `HTSeq-count` to determine which genes each aligned read comes from. This is a much slower process than psuedoalignment, but is more accurate.

#### Normalizing Counts

The counts for each gene in each sample cannot be directly compared because factors like the number of reads sequenced, the complexity of the cDNA library, and the length of the gene will all affect how many reads our counted for a gene without implying a meaningful difference in gene expression. To correct for these discrepencies we use normalized counts for our analysis. Transcripts per Million (TPM), Read Fragments per Kilobase Million (RFKM), and Counts per Million (CPM) are all common normalization methods, but the best method is using variance normalized counts, which we generate with a program called `DESeq`. DESeq takes in the count matrix, and computes the normalized counts which we can use for our analysis.

#### Dimensionality Reduction

Principal Component Analysis (PCA) is a method of visualizing the variance in a dataset. This method applies an orthogonal linear transformation to the data in order to transform the data into a new coordinate system where the greatest variance is projected onto the first coordinate (the first principal component), the second greatest variance onto the second coordinate, and so on. This allows us to visualize the variance in our data in a way that is easy to interpret.

t-Stochasic Neighbor Embeddings (tSNE) is a method of transforming highly-dimensional data into a fixed number of dimensions (usually 2 or 3) in a way that "preserves the local structure of the data." This is done by creating a *similarity matrix* between all data points by calculating the euclidean distance between the points in the *n* dimensional space, and this matrix is then scaled using a t-distribution which increases value of the smaller (local) distances. The data are then randomly scattered into the lower dimensional space and the euclidean distances are recalculated. The datapoints are then moved iteratively until the data are in a configuration that minimizes the difference in the similarity matrices between the high and low dimensional spaces. The iteration process becomes VERY slow when trying to project onto many dimensions.

Uniform Manifold Approximation and Projection (UMAP) is a similar method to tSNE, but is faster and more scalable. UMAPs use a k-nearest neighbors approach to create a 'fuzzy simplicial complex', which is a graph structure that connects the data points with edges, weighted by the likelihood that the two points are truly 'neighbors'. I will get hand-wavy and say that they use this structure like the tSNEs similarity matrix, by trying to replicate the graphs topoly by projecting the data onto a lower dimensional space. UMAPs are significantly faster that tSNEs but have more tunable hyperparameters.

**NOTE:** tSNE and UMAP are both non-linear transformations of data, which actually convert the data points into new dimensions. Because of this the interpretations of tSNE and UMAP plots are not straightforward, or intuitive, and it is very easy to make misleading plots with them. They also have many outspoken critics who claim that they are in fact useless for analysis, and that their results are not truly representative of a dataset since selecting for "local" and "distal" structures is not mathematically sound.

#### Differential Expression Analysis

With RNA data, it is meaningful to look at the differences in gene expression that occurs between two conditions of samples. Thankfully, DESeq is the standard of differential expression analysis, and we already use it to normalize counts. DESeq uses a negative binomial distribution to model the counts of each gene, and then uses a generalized linear model to determine which genes are differentially expressed between two conditions. DESeq can also use a method called shrinkage estimation to reduce the variance of the estimates of gene expression, which is especially useful when you have a small number of samples.

#### Gene Ontology or Gene Set Enrichment

GO and GSEA are methods of determining if any pathways or gene sets are enriched in a set of genes.

GO is a method of determining enrichment of pathways from a small set of genes. GSEA is a method of determining enrichment of of pathways from a weighted set of genes. I find GSEA to be less biased, and more informative, but it is also less powerful (in that it theoretically gives less profound results).

I prefer GSEA, so we will start with that.
For GSEA we need to weight all of our genes using the differential expression information from DESeq. 

The easiest way to determine dysregulation of a gene is to look at the log of the p-values produced. Because p-values are always positive, highly upregulated **and** highly downreguated genes will both have very high `log(p-val)`. The "enriched" pathways will then be the ones with the most extreme dysregulation.

However sometimes we don't actually care about broad 'dysregulation'. We want things to be enriched or depleted, upreulated or downregulated. We could use the log fold change of the genes, but that becomes problematic as low abundance genes will likely have much more variable fold changes (hence why we always look at p-values). Instead, we use the statistic produced by DESeq. This statistic is called the Wald statistic. It is generated with the log fold change and base mean of a gene, and is how the p-value is produced. When we use the Wald statistic, we are looking at the most extreme changes in expression while incorporating the direction of change. This means that from the GSEA we can see gene sets and pathways that are upregulated or downregulated, not just dysregulated.
