---
layout: post
title:  "Script for testing several k-mers"
date:   2017-12-06 17:00:00
categories: main
---

Yesterday, I made a script that would automatically run `ABySS` for different k-mers, create a new directory for each k-mer and put the output files in that directory, and also remove all "big sized" files which is primarily those ending with `.dot` and `.fa`. As I am only interested in the statistics (e.g. E-size values and N50), I only want to keep the stats-files. This is the script (now run with the untrimmed, raw reads, and with the `.gz`-file, i.e. not the unzipped to save memory):

```bash
#!/bin/sh
# Script for assembling genome of P. andina
# How to run:
# sbatch -A g2017020 -t 48:00:00 -p node -n 8 -o p-andina_assembly.fa raw-multiple-kmers.sh

mkdir -p /home/ellinor/Project/testrun
cd /home/ellinor/Project/testrun

# modules needed
module add bioinfo-tools
module add abyss/2.0.2-k128

for k in `seq 31 8 90`; do
  cd /home/ellinor/Project/testrun
  mkdir -p k$k
  abyss-pe -C k$k name=pandina-untrimmed k=$k se=/home/ellinor/Project/SRR1817238.fastq.gz
  find /home/ellinor/Project/testrun/k$k -regex '.*.\(fa\|dot\)$' -exec readlink -f {} \; | xargs rm -f
done
```

For example, with 5 completed k-mer sizes, I now only use 11 Gb (including the actual sequence file) instead of approximately 160 Gb. 

I will update tomorrow when all k-mers have been tested. Notice that the script will use every 8th k-mers from 31 to 90, but we realized that we should also test k-mers below 31. Therefore, Maria will run an assembly on k-mer size 23, and Panos will test with k-mer 15 and 7 which will be our smallest k-mer. After that, we aim to plot k-mer sizes against N50 and E-size, respectively. The reasons for using those is primarly because we at this point know most about them, and we have read about them in articles and heard about them during the lectures. At the time of writing, I have also been told from another group that was meeting with Lars Arvestad, that using N50 and E-size is a good way to go.

I will also run the assembly again, the same way, but with the trimmed data obtained by using `sickle` and plot the results there too. We expect that the trimmed data will generate a higher quality assembly of the contigs. 

Today I also e-mailed Lars Arvestad and asked for a meeting so that we can discuss the problems we have so far and if he has any suggestions on how to proceed, or if what we are doing is a good approach. 


















