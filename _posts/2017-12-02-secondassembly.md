---
layout: post
title:  "Assembly try #2"
date:   2017-12-02 12:00:00
categories: main
---

Yesterday, the run was cancelled due to an error with writing `unitigs1` in the abyss-run: 

```bash
abyss-pe k=48 se="SRR1817238.fastq" name=p-andina-assembly-untrimmed "unitigs1"
```

I also experienced a problem with not having enough space on my home directory, where I had exceeded the quota of 32 Gb and had data of ~38 Gb. This is because the FASTQ-file with raw sequencing data is 16Gb, and the trimmed version of data (using sickle) is 14Gb. I therefore downloaded the trimmed version locally to my computer for now, until the assembly of the original FASTQ-file is working. When it is working, the trimmed version will be uploaded and run with ABySS. 

A new job was submitted (10 hours run) where the `unitigs1` part was removed: 

```bash
#!/bin/sh

cd /home/ellinor/Project

# modules needed
module add bioinfo-tools
# module add python
module add abyss/2.0.2-k128

# Assembly using ABySS
# se="" takes only single-reads as input, even though we run the abyss-pe mode
abyss-pe k=48 se="SRR1817238.fastq" name=p-andina-assembly-untrimmed
```

And from what I can see today it seems to have worked, but I only base this on the fact that I have the expected output-files in the directory. However we are missing a `.contig`- and a `.scaffold`-file which another group got that used ABySS but in that case they had paired-end reads, and this does not have to mean the assembly did not work. When looking at the `.out`-file, no error messages are found. The statistics from the assembly can be seen below. 

```
$ cat p-andina-assembly-untrimmed-unitigs.fa 
n        n:500  L50    min  N80  N50  N20   E-size  max   sum      name  
2840597  43385  13871  500  616  897  1525  1133    5973  38.34e6  p-andina-assembly-untrimmed-unitigs.fa 
```

The quality of the assembly will be assessed the next coming days by running several different k-mers, and also use the trimmed sequences obtained using `sickle`. But as it seems now, no scaffolding has been performed (even though we have read that ABySS has an in-built scaffolding tool), and this means we will have to look into different scaffolding tools. But that's for Monday. 







