# TNC_project
## Introduction
In this project, we are going to build the OTU table based on the raw `<*.fasq>` sequences dataset. 

## Data processing for raw dataset

### [FastQC](https://www.bioinformatics.babraham.ac.uk/projects/fastqc/)

FastQC aims to provide a simple way to do some quality control checks on raw sequence data coming from high throughput sequencing pipelines. It provides a modular set of analyses which you can use to give a quick impression of whether your data has any problems of which you should be aware before doing any further analysis.

We use FastQC to do see the quality of the raw dataset. Run `<test_qc_one>` to see FastQC reports in the file `<fq_report>`.


## Cutadapt on primers and parts showing low quality for each sequence.
###[cutadapt](https://cutadapt.readthedocs.io/en/stable/)



Once you had a closer look at the quality report you can realize that the data quality is not too bad, however we still might be able to improve the quality with a quality based trimming since the quality drops towards the end of the reads. In this project, we uses cut adapt to 
## Use PIPITS to do OTU study based on trimmed sequences data.
After trimming and cleaning the raw sequences according FastQC report. We are going to use PIPITS to make OTU table.
