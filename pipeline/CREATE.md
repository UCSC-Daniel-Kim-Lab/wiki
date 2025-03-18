# CREATE
## 18 MAR 2025
### Alex D Hill

#### Comprehensive Repetitive Element And Transposable Element sequencing

CREATE is the pipeline that I (Alex D Hill) have developed as a successor to COMPLETE-seq which was pioneered by Roman E Reggiardo and Vikas Peddu.

The core idea was to make a streamlined, easy-to-use pipeline that allowed us to analyze the RNA from both genes and repetitive elements in the same dataset.  The first version of COMPLETE-seq used a custom designed reference index for `Salmon` and a few bash scripts to do the data processing. This was then ported into a`Nextflow` pipeline that allowed the running of multiple samples to be much easier.

CREATE has built upon these building blocks in a few ways. 

  1. CREATE has a more dynamic refernce builder
    - CREATE have a `--reference` mode that will allow you to develop a reference directory for custom gene annotations, any version of GENCODE's annotated release, and for the release of the T2Tv2 reference genome. This allows for more flexibility by the user so that the pipeline won't be made easily out of date, and it allows us to take advantage of the T2T reference genome which in it self has a significant amount more repeat content.
  2. CREATE is compatible with HPCs
    - I have added support for slurm job submission to automate the process of batching add submitting jobs on an HPC. This makes it much more convenient for inexperienced users to use CREATE effectively and has increased our throughput dramatically (A job which took over two weeks when I joined the lab can now be done in several hours if run graciously).
  3. CREATE outputs worthwhile analysis and can be run in a highly-efficient format
    - CREATE now outputs a series of basic plots and data tables containing worthwhile comparisons of data based on a metadata sheet submitted with the workflow. It also makes intermediate files optional and outputs the quantifications of all samples and their metadata, and information about the genes and biotypes of each quantified transcript in a very small H5 object using the HDF5Array package in R. This means that even for large datasets the output will be only a few hundred megabytes.


