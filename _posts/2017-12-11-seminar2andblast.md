---
layout: post
title:  "Seminar 2 done + BLAST update"
date:   2017-12-11 16:00:00
categories: main
---
During Seminar 2 today, we presented the following presentation. 

{% include google_slide_seminar_2.html %}

After listening to the presentations by other groups, it seems like we are in phase regarding how much we have done and how many tasks we have finished. We did not receive any feedback or questions, and since we have single reads, are project stands out a bit from the otherwise similar projects (for example there are two groups using `ABySS`, but they have paired ends). 

### Using BLAST to investigate gene sequences

Initially when using BLAST and testing different tools (specifically when using tblastn and tblastx) we got the following error: 

{% include image.html
            img="assets/images/blasterror.png"
            title="blasterror"
            caption="Error message when using BLAST with the gene sequences and the contig-file obtained from ABySS"
            url="" %}

It seems like the error is due to the usage of a too large genome file (our contig-file from ABySS). So first we need to find a way to search for the genes in the whole file, or basically divide the file into several different files and BLAST them separately with blastx. Notice that this is when using the webtool. 

When running it "locally" by first creating a database, it seems to be working. First, I created the database with the CHS-genes: 

```bash 
[ellinor@rackham3 Project]$ makeblastdb -in chs.fa -dbtype prot -out chs-genes-db
```

And three files are created: 
`chs-gene-db.phr`, `chs-genes-db-pin`, and `chs-genes-db.psq`.

Then, the following script is used with `blastx` and the contig-file as obtained with `ABySS`: 

```bash
#!/bin/sh
# How to run:
# sbatch -A g2017020 -t 10:00:00 -p node -n 8 -o blastresults.out blast.sh

cd /home/ellinor/

# modules needed
module add bioinfo-tools
module add blast/2.6.0+

blastx -query assembled-unitigs.fa -db chs-genes-db -out chscontigsblast -outfmt 6
```

I decided to run all genes instead from just a few species as it's very simple. But we will probably divide the analysis of the results between us. Notice that the output format is set to 6 as this should give just a simple, tabular output format which is easier to interpret. 

The job is submitted on UppMax and running at the time of writing. I will update when the results have been obtained! 

### Weekly plan

We decided to have our next group meeting on Wednesday the 13th of December, and by then, we will have run BLAST and obtained results, as well as analyzing them. Me and Maria have run BLAST on all genes with `-outfmt 6`while Panos has run on only the *P. infestans*-genes with `-outfmt 5`. We also decided to look into `MUMmer`on Wednesday and perhaps continue and Friday depending on if we have problems or not :grin:
