# Phoenix High-Performance Computing System
### Alex D. Hill
#### 30 JAN 2025

#### HPC's and SLURM
HPC systems are not the same as the other GI servers where you can just log on and start working as if you were running on your own computer. HPCs have enormous numbers of CPUs available and tons of memory, but it is spread across 'clusters' which are essentially their own servers. Phoenix has 22? servers in the cluster, some of which also have access to GPUs which is useful for certain processes like basecalling nanopore data.

The way we interact with the HPC is through a program called SLURM. SLURM is a "job scheduler" that allows us to submit jobs to the cluster and have them run in the background. This allows the many users at UCSC to submit their jobs to SLURM, and it will handle the resource management to ensure that everybody's processes run smoothly.

SLURM does this through 'queues' which are (here at UCSC) determined by the amount of time you give your job to run. the `short` queue is for all jobs that take less than 1 hour, the `medium` queue is for jobs that take less than 24 hours, and the `long` queue is for jobs that take less than 7 days. There are also a special `gpu` queue for jobs that require access to the GPU servers.

You submit jobs to slurm by creating a 'job script', which is essentially just a bash script that tells SLURM what commands you want to run. In this script however, you can also add special comments to the top that tell SLURM how to run your job. Here is a dummy example of one such job script that runs a python script:
```bash
#!/bin/bash
#SBATCH --job-name=example_job
#SBATCH --output=example_job.out
#SBATCH --error=example_job.err
#SBATCH --time=1:00:00
#SBATCH --partition=short
#SBATCH --ntasks=1
#SBATCH --cpus-per-task=8
#SBATCH --mem=32G
python example_script.py
```

`sbatch` is the command that gives a job script to SLURM for running. the `#SBATCH` lines pass arguments to the `sbatch` command telling it how much time/memory/CPUs/etc. to allocate to your job. I woulod submit the above script with the command:
```
sbatch example_job.sh
```

Once your job is running, you can check on it (and all other jobs currently running in SLURM) by using the squeue command. I use a special alias `alias squeue='squeue --format="%8u->[%.7T](%3P)%10N:%10i[%3C|%7m|%.10M/%10L] %$(( $COLUMNS - 81 ))j" --sort="-T,u,-C"` to specially format the output like below:
```bash
USER    ->[  STATE](PAR)NODELIST  :JOBID     [CPU|MIN_MEM|      TIME/TIME_LEFT ] NAME
ahill6  ->[RUNNING](sho)phoenix-00:1234567   [8  |128G   |  00:20:43/  00:04:17] example_job
```
In the above example I have a job named example_job, which is using 8 CPUs, 128GBs of memory, and has been running for 20 minutes with 4 minutes left. I can see which of the servers in the cluster it is using (phoenix-00), and it was given a job ID of 1234567. It also shows the userID (ahill6) of the person that submitted the job to run.

When a job has finished running, you will sometimes need to see the exit status of the job to make sure it ran and did not crash. To monitor this information, you can use the command `sacct`. Like `squeue` I use a special format alias to make the `sacct` command more informative [`alias sacct='sacct --format="NodeList%10,JobID%9,JobName%75,Partition%4,ExitCode%4,State%7,AllocCPUS%4,MaxRSS%10,ReqMem%5,Elapsed%10,Time%10"'`]. Here is an example of my `sacct` output:
```bash
  NodeList     JobID                                                                     JobName Part Exit   State Allo     MaxRSS ReqMe    Elapsed  Timelimit 
---------- --------- --------------------------------------------------------------------------- ---- ---- ------- ---- ---------- ----- ---------- ---------- 
phoenix-15   6107498                            nf-CREATE_REFERENCE_download_gencode_transcripts sho+  0:0 COMPLE+    1               1G   00:04:02   00:10:00 
phoenix-15 6107498.+                                                                       batch       0:0 COMPLE+    1      9528K         00:04:02            
phoenix-15   6107499                             nf-CREATE_REFERENCE_download_gencode_annotation sho+  0:0 COMPLE+    1               1G   00:04:32   00:10:00 
phoenix-15 6107499.+                                                                       batch       0:0 COMPLE+    1      9480K         00:04:32            
...
```

The important details here are the JobIDs and Job Names which tell you what processes you are looking at, and the Exit code section which will tell you if the job was killed. I also like to print the memory used (MaxRSS), and the elapsed time to see whether the job took too much time or memory.

`sacct` actually only shows the jobs that you launched since 0:00 on the current day. If you ran a job the day before and want to check its exit status, you need to use the `--starttime/-S` flag. To see all jobs you have run since Jan 2, 2025 for example, you would use the command:
```
sacct -S 01/02/25
```