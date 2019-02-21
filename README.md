# Battenberg
-----

## Advised installation and running instructions

Please visit the [cgpBattenberg page](https://github.com/cancerit/cgpBattenberg), download the code from there and run the setup.sh script. The battenberg.pl script can then be used to run the pipeline.


## Advanced installation instructions

The instructions below will install the latest stable Battenberg version. Please take this approach only when you'd like to do something not covered by cgpBattenberg.

## Description of the output

Battenberg produces a number of output files. The copy number profile is saved in a file that ends with `_subclones.txt`, which is a tab delimited file in BED format. Within this file there is a line for each segment in the tumour genome.
Each segment will have either one or two copy number states:

    If there is one state that line represents the clonal copy number (i.e. all tumour cells have this state)
    If there are two states that line represents subclonal copy number (i.e. there are two populations of cells, each with a different state)

A copy number state consists of a major and a minor allele and their frequencies, which together add give the total copy number for that segment and an estimate fraction of tumour cells that carry each allele.

The following columns are available in the Battenberg output:

  * chr - The chromosome of the segment.
  * startpos - Start position on the chromosome.
  * endpos - End position on the chromosome.
  * BAF - The B-allele frequency of the segment.
  * pval - P-value that is obtained when testing whether this segment should be represented by one or two states. A low p-value will result in the fitting of a second copy number state.
  * LogR - The log ratio of normalised tumour coverage versus its matched normal sequencing sample.
  * ntot - An internal total copy number value used to determine the priority of solutions. NOTE: This is not the total copy number of this segment!
  * nMaj1_A - The major allele copy number of state 1 from solution A.
  * nMin1_A - The minor allele copy number of state 1 from solution A.
  * frac1_A - Fraction of tumour cells carrying state 1 in solution A.
  * nMaj2_A - The major allele copy number of state 2 from solution A. This value can be NA.
  * nMin2_A - The minor allele copy number of state 2 from solution A. This value can be NA.
  * frac2_A - Fraction of tumour cells carrying state 2 in solution A. This value can be NA.
  * SDfrac_A - Standard deviation on the BAF of SNPs in this segment, can be used as a measure of uncertainty.
  * SDfrac_A_BS - Bootstrapped standard deviation.
  * frac1_A_0.025 - Associated 95% confidence interval of the bootstrap measure of uncertainty.
  * frac1_A_0.975 - Associated 95% confidence interval of the bootstrap measure of uncertainty.

Followed by possible equivalent solutions B to F with the same columns as defined above for solution A (due to the way a profile is fit Battenberg can generate a series of equivalent solutions that are reported separately in the output).

### Standalone

#### Prerequisites

Installing from Github requires devtools and Battenberg requires readr, RColorBrewer and ASCAT. The pipeline requires doParallel. From the command line run:

```
R -q -e 'source("http://bioconductor.org/biocLite.R"); biocLite(c("devtools", "readr", "doParallel", "ggplot2", "RColorBrewer", "gridExtra", "gtools"));'
R -q -e 'devtools::install_github("Crick-CancerGenomics/ascat/ASCAT")'
```

#### Installation from Github

To install Battenberg, run the following from the command line:

```
R -q -e 'devtools::install_github("Wedge-Oxford/battenberg")'
```

#### Required reference files

Battenberg requires a number of reference files that should be downloaded.

  * ftp://ftp.sanger.ac.uk/pub/teams/113/Battenberg/battenberg_1000genomesloci2012_v3.tar.gz
  * ftp://ftp.sanger.ac.uk/pub/teams/113/Battenberg/battenberg_impute_1000G_v3.tar.gz
  * ftp://ftp.sanger.ac.uk/pub/teams/113/Battenberg/probloci_270415.txt.gz
  * ftp://ftp.sanger.ac.uk/pub/teams/113/Battenberg/battenberg_wgs_gc_correction_1000g_v3.tar.gz
  * ftp://ftp.sanger.ac.uk/pub/teams/113/Battenberg/battenberg_snp6_exe.tgz (SNP6 only)
  * ftp://ftp.sanger.ac.uk/pub/teams/113/Battenberg/battenberg_snp6_ref.tgz (SNP6 only)
  
#### Pipeline

Go into inst/example for example WGS and SNP6 R-only pipelines.
  
### Docker - experimental

Battenberg can be run inside a Docker container. Please follow the instructions below.

#### Installation

```
git clone git@github.com:Wedge-Oxford/battenberg.git
cd battenberg
docker build -t battenberg:2.2.8 .
```

#### Download reference data

To do

#### Run interactively

These commands run the Battenberg pipeline within a Docker container in interactive mode. This command assumes the data is available locally in `$PWD/data/pcawg/HCC1143_ds` and the reference files have been placed in `$PWD/battenberg_reference`

```
docker run -it -v `pwd`/data/pcawg/HCC1143_ds:/mnt/battenberg/ -v `pwd`/battenberg_reference:/opt/battenberg_reference battenberg:2.2.8
```

Within the Docker terminal run the pipeline, in this case on the ICGC PCAWG testing data available [here](https://s3-eu-west-1.amazonaws.com/wtsi-pancancer/testdata/HCC1143_ds.tar).

```
R CMD BATCH '--no-restore-data --no-save --args HCC1143 HCC1143_BL /mnt/battenberg/HCC1143_BL.bam /mnt/battenberg/HCC1143.bam FALSE /mnt/battenberg/' /usr/local/bin/battenberg_wgs.R /mnt/battenberg/battenberg.Rout
```
  
### Building a release

In RStudio: In the Build tab, click Check Package

Then open the NAMESPACE file and edit:

```
S3method(plot,haplotype.data)
```  

to:

```
export(plot.haplotype.data)
```
