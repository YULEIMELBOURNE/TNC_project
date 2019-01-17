# :evergreen_tree: TNC_project :mushroom: 


## References
[One big biome table](https://www.biorxiv.org/content/biorxiv/suppl/2017/09/10/184960.DC1/184960-2.pdf)

[Primer sequences](https://nature.berkeley.edu/brunslab/tour/primers.html)

## Dataset Introduction
In this project, we got the metagenomics data using Illumina MiSeq System. Based on the raw dataset, we are going to following works:


- [x] Data Processing
- [x] Build the complete OTU Table
- [x] Get a fairly good idea of what factors are driving the fungal communities vs ectomycorrhizal fungal communities.
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
00:57 101Mb   100.0% 97.3% merged
                                 
Merging
  Fwd ../fq_trimmed_test/01N_T0_R1.fastq
  Rev ../fq_trimmed_test/01N_T0_R2.fastq
  Relabel reads as 01N.#

01:48 101Mb   100.0% 97.1% merged
                                 
Merging
  Fwd ../fq_trimmed_test/01S_T0_R1.fastq
  Rev ../fq_trimmed_test/01S_T0_R2.fastq
  Relabel reads as 01S.#

02:12 101Mb   100.0% 96.1% merged
                                 
Merging
  Fwd ../fq_trimmed_test/01W_T0_R1.fastq
  Rev ../fq_trimmed_test/01W_T0_R2.fastq
  Relabel reads as 01W.#

03:17 101Mb   100.0% 96.5% merged

Totals:
   1001786  Pairs (1.0M)
    966903  Merged (966.9k, 96.52%)
    549979  Alignments with zero diffs (54.90%)
      9975  Too many diffs (> 15) (1.00%)
         1  Fwd tails Q <= 2 trimmed (0.00%)
         2  Rev tails Q <= 2 trimmed (0.00%)
       891  Fwd too short (< 64) after tail trimming (0.09%)
       719  Rev too short (< 64) after tail trimming (0.07%)
      6932  No alignment found (0.69%)
         0  Alignment too short (< 16) (0.00%)
      3065  Merged too short (< 180)
     13301  Merged too long (> 320)
        85  Staggered pairs (0.01%) merged & trimmed
    194.76  Mean alignment length
    260.01  Mean merged length
      0.71  Mean fwd expected errors
      0.63  Mean rev expected errors
      0.11  Mean merged expected errors
```
The output for `.\filter`

```bash
00:00 38Mb   CPU has 32 cores, defaulting to 10 threads
04:07 33Mb    100.0% Filtering, 97.4% passed
    966903  Reads (966.9k)                  
     25090  Discarded reads with expected errs > 1.00
    941813  Filtered reads (941.8k, 97.4%)
```

The output for `./uniques`
```bash
00:07 317Mb   100.0% Reading ./out/filtered.fa
00:07 283Mb  CPU has 32 cores, defaulting to 10 threads
00:13 502Mb   100.0% DF
00:13 513Mb  941813 seqs, 192986 uniques, 154096 singletons (79.8%)
00:13 513Mb  Min size 1, median 1, max 47075, avg 4.88
00:18 352Mb   100.0% Writing ./out/uniques.fa
```

The output for `./otus`
```bash
00:28 52Mb    100.0% 606 OTUs, 190 chimeras
```

The output for `./otutable `

```bash
00:00 41Mb    100.0% Reading ./out/otus.fa
00:00 7.2Mb   100.0% Masking (fastnucleo) 
00:00 8.0Mb   100.0% Word stats          
00:00 8.0Mb   100.0% Alloc rows
00:00 8.6Mb   100.0% Build index
00:00 42Mb   CPU has 32 cores, defaulting to 10 threads
04:38 81Mb    100.0% Searching merged.fq, 99.1% matched
957618 / 966903 mapped to OTUs (99.0%)                 
04:38 81Mb   Writing ./out/otutable.txt
04:38 81Mb   Writing ./out/otutable.txt ...done.
```
* Parameter information for our pipeline:
```bash

*Trimme Sequences:*
-Forward Primmer: 
GATCTCTTGGNTCTNGCATCGATGAAGAACG
-Forward cut end length: 25
-Forward q = 20; e = 0.2

-Reverse Primmer: GGAAACCTTGTTACGACTTTTACTTCCTCTAAATGACCAA
-Reverse cut end length: 30
-Reverse q = 20; e = 0.2

*Usearch*
-Merge: 
fastq_maxdiffs: 15; -fastq_pctid: 15;
fastq_minmergelen 200; -fastq_maxmergelen 350

-filter:
fastq_maxee 0.5

-otus:
minsize 2

-otutable:
id 0.97

-prediction:
database: unitITS.udb
cutoff: 0.8 

```
