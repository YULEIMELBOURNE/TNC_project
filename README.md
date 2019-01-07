# TNC_project
## Introduction
In this project, we are going to build the OTU table based on the raw .fasq sequences dataset. 
## Data processing for raw dataset
We use FastQC to do see the quality of the raw dataset.
## Cutadapt to cut tails for each seuqneces
Once you had a closer look at the quality report you can realize that the data quality is not too bad, however we still might be able to improve the quality with a quality based trimming since the quality drops towards the end of the reads. In this project, we uses cut adapt to 
