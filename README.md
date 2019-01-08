# :evergreen_tree: TNC_project :mushroom: 
## Dataset Introduction
In this project, we got the metagenomics data using Illumina MiSeq System. Based on the raw dataset, we are going to following works:

- [ ] Data Processing
- [ ] Build the complete OTU Table
- [ ] Get a fairly good idea of what factors are driving the fungal communities vs ectomycorrhizal fungal communities.
- [ ] Study the outcome of fungal communitites after forest burning.


# Data processing for raw dataset

## [FastQC](https://www.bioinformatics.babraham.ac.uk/projects/fastqc/)

FastQC aims to provide a simple way to do some quality control checks on raw sequence data coming from high throughput sequencing pipelines. It provides a modular set of analyses which you can use to give a quick impression of whether your data has any problems of which you should be aware before doing any further analysis.

We use FastQC to do see the quality of the raw dataset. Run `"test_qc_one"` to see FastQC reports in the file `"fq_report"`.


## [Cutadapt](https://cutadapt.readthedocs.io/en/stable/) on primers and parts showing low quality for each sequence.

Once you had a closer look at the quality report you can realize that the data quality is not too bad, however we still might be able to improve the quality with a quality based trimming since the quality drops towards the end of the reads. Additionally, we want to remove primers before we do OTU study. 

Run `"code_trimme"` to do this step and we save trimmed sequences in file `"fq_trimmed"`.

## Run FastQC on trimmed data.
In order to ccompare the different before and after Cutadapt, we run fastQC again on `"fq_trimmed"` file. Then show the comparesion in [Figure1](/result.pdf). From the changes in sequences' length and number, we see cutadapt help us remove primers and sequences with low quality.


## Use PIPITS to do OTU study based on trimmed sequences data.
After trimming and cleaning the raw sequences according FastQC report. We are going to use PIPITS to make OTU table.
