# :evergreen_tree: TNC_project :mushroom: 

## Questions

1. We removed barcodes?
2. Demultiplex?
3. There is one warning after we run cutadapt for forward sequences but not for reverse sequences. I used TCCGTAGGTGAACCTGCGG for forward sequences)

```bash
WARNING:
    The adapter is preceded by "T" extremely often.
    The provided adapter sequence could be incomplete at its 3' end.
```

## References
[One big biome table](https://www.biorxiv.org/content/biorxiv/suppl/2017/09/10/184960.DC1/184960-2.pdf)

[Primer sequences](https://nature.berkeley.edu/brunslab/tour/primers.html)

## Dataset Introduction
In this project, we got the metagenomics data using Illumina MiSeq System. Based on the raw dataset, we are going to following works:

- [ ] Data Processing
- [ ] Build the complete OTU Table
- [ ] Get a fairly good idea of what factors are driving the fungal communities vs ectomycorrhizal fungal communities.
- [ ] Study the outcome of fungal communitites after forest burning.

Note: All of codes are made and tested under mac and linux.

# Data processing for raw dataset

## [FastQC](https://www.bioinformatics.babraham.ac.uk/projects/fastqc/)

FastQC aims to provide a simple way to do some quality control checks on raw sequence data coming from high throughput sequencing pipelines. It provides a modular set of analyses which you can use to give a quick impression of whether your data has any problems of which you should be aware before doing any further analysis.

We use FastQC to do see the quality of the raw dataset. Run `"test_qc_one"` to see FastQC reports in the file `"fq_report"`.
```bash
./test_qc_one
```

## [Cutadapt](https://cutadapt.readthedocs.io/en/stable/) on primers and parts showing low quality for each sequence.

Once you had a closer look at the quality report you can realize that the data quality is not too bad, however we still might be able to improve the quality with a quality based trimming since the quality drops towards the end of the reads. Additionally, we want to remove primers before we do OTU study. 

Run `"code_trimme"` to do this step and we save trimmed sequences in file `"fq_trimmed"`.
```bash
./code_trimme
```

## Run FastQC on trimmed data.
In order to ccompare the different before and after Cutadapt, we run fastQC again on `"fq_trimmed"` file. Then show the comparesion in [Figure1](/result.pdf). From the changes in sequences' length and number, we see cutadapt help us remove primers and sequences with low quality.


## Use [PIPITS](https://github.com/hsgweon/pipits) to do OTU study based on trimmed sequences data.
After trimming and cleaning the raw sequences according FastQC report. We are going to use PIPITS to make OTU table. 
We run `"run_PIPITS"` to generate the OTU table.
```bash
./run_PIPITS
```

There is one error when we run PIPITS on cluster but won't happen on laptop. We have already reflect this problem to the writer. 

**_When we test PIPITS on [sample dataset](rawdata), it works well and gives us the finally [OTU table](otu_table_from_PIPITS_sample.txt) in reasiable time. But when we run the program on our dataset. We found the time cost is too large. We waited for about an hour but cannot get the result (Only 01W, 01S, 01N, 01E) comparing with Usearch which can finish in 30 mins for whole dataset (01-15W, 01-15S, 01-15N, 01-15E)._**


### Conclusion
Maybe PIPITS is a good program for small size dataset but it should be considered when our dataset is large.

## [Usearch](http://www.drive5.com/usearch/)

We are going to separate whole dataset into two group then run Usearch on each group, then we can use the commond `otutab2biom command` to transform `.txt` into `.json` file. Next we are going to try to run program [merge_otu_tables.py](http://qiime.org/scripts/merge_otu_tables.html) (a module in QIIME) to merge two OTU tables together. 

To increase way, there are three approach:
- Cut more end bases
- Change the parameter `fastq_maxdiffs` and `fastq_pctid`
- Change the parameter `fastq_minmergelen` and `fastq_maxmergelen` 

Then we set `fastq_minmergelen 180`, `fastq_maxmergelen 320`, `fastq_maxdiffs 20` and `fastq_pctid 20` and get high merge rate as following: 

```bash
usearch v11.0.667_i86linux32, 4.0Gb RAM (32.8Gb total), 32 cores
(C) Copyright 2013-18 Robert C. Edgar, all rights reserved.
https://drive5.com/usearch

License: lyu062@ucr.edu


Merging
  Fwd ../fq_trimmed_test/01E_T0_R1.fastq
  Rev ../fq_trimmed_test/01E_T0_R2.fastq
  Relabel reads as 01E.#

00:00 72Mb   FASTQ base 33 for file ../fq_trimmed_test/01E_T0_R1.fastq
00:00 72Mb   CPU has 32 cores, defaulting to 10 threads
00:48 101Mb   100.0% 97.8% merged 
                                 
Merging
  Fwd ../fq_trimmed_test/01N_T0_R1.fastq
  Rev ../fq_trimmed_test/01N_T0_R2.fastq
  Relabel reads as 01N.#

01:31 101Mb   100.0% 97.7% merged
                                 
Merging
  Fwd ../fq_trimmed_test/01S_T0_R1.fastq
  Rev ../fq_trimmed_test/01S_T0_R2.fastq
  Relabel reads as 01S.#

01:58 101Mb   100.0% 96.6% merged
                                 
Merging
  Fwd ../fq_trimmed_test/01W_T0_R1.fastq
  Rev ../fq_trimmed_test/01W_T0_R2.fastq
  Relabel reads as 01W.#

03:02 101Mb   100.0% 97.0% merged

Totals:
   1001786  Pairs (1.0M)
    971860  Merged (971.9k, 97.01%)
    549979  Alignments with zero diffs (54.90%)
      4927  Too many diffs (> 20) (0.49%)
         1  Fwd tails Q <= 2 trimmed (0.00%)
         2  Rev tails Q <= 2 trimmed (0.00%)
       891  Fwd too short (< 64) after tail trimming (0.09%)
       719  Rev too short (< 64) after tail trimming (0.07%)
      6932  No alignment found (0.69%)
         0  Alignment too short (< 16) (0.00%)
      3071  Merged too short (< 180)
     13386  Merged too long (> 320)
        85  Staggered pairs (0.01%) merged & trimmed
    194.64  Mean alignment length
    260.04  Mean merged length
      0.73  Mean fwd expected errors
      0.65  Mean rev expected errors
      0.13  Mean merged expected errors

```
