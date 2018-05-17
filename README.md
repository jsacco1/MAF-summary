# MAF-summary

Set of scripts to summarise, analyse and visualise multiple [Mutation Annotation Format](https://software.broadinstitute.org/software/igv/MutationAnnotationFormat) (MAF) files using *[maftools](https://www.bioconductor.org/packages/devel/bioc/vignettes/maftools/inst/doc/maftools.html)* R package. The maftools manuscript is on [bioRxiv](http://dx.doi.org/10.1101/052662) and scripts are available on [GitHub](https://github.com/PoisonAlien/maftools).


## Table of contents

<!-- vim-markdown-toc GFM -->
* [MAF field requirements](#maf-field-requirements)
* [Data and files](#data-and-files)
* [Scripts](#scripts)
* [Converting ICGC Simple Somatic Mutation Format to MAF](#converting-icgc-simple-somatic-mutation-format-to-maf)
* [Summarising and visualising multiple MAF files](#summarising-and-visualising-multiple-maf-files)

<!-- vim-markdown-toc -->


## MAF field requirements

While MAF files contain many fields ranging from chromosome names to cosmic annotations, the mandatory fields used by maftools are the following:

Field | Description | Allowed values
------------ | ------------ | ------------
Hugo_Symbol | [HUGO](https://www.genenames.org/) gene symbol | -
Chromosome | Chromosome number | 1-22, X, Y
Start_Position | Start position of event | numeric
End_Position | End position of event | numeric
Reference_Allele | The plus strand reference allele at this position | A, T, C, G
Tumor_Seq_Allele2 | Primary data genotype | A, T, C, G
Variant_Classification | Translational effect of variant allele | Frame_Shift_Del, Frame_Shift_Ins, In_Frame_Del, In_Frame_Ins, Missense_Mutation, Nonsense_Mutation, Silent, Splice_Site, Translation_Start_Site, Nonstop_Mutation, 3'UTR, 3'Flank, 5'UTR, 5'Flank, IGR, Intron, RNA, Targeted_Region, De_novo_Start_InFrame, De_novo_Start_OutOfFrame, Splice_Region, Unknown
Variant_Type | Variant Type | SNP, DNP, INS, DEL, TNP and ONP
Tumor_Sample_Barcode | Sample ID, either a TCGA barcode, or for non-TCGA data, a literal SAMPLE_ID as listed in the clinical data file | -
<br />


## Data and files

The MAF files for  are conducted on [Spartan](https://dashboard.hpc.unimelb.edu.au/) cluster and are located in the following directory:<br>

*/data/cephfs/punim0010/projects/Jacek/**Pancreatic1500_Atlas**/data*
<br>

Cohort | Samples number | NCBI Build | File name (original) | File name (modified) | Comments
------------ | ------------ | ------------ | ------------ | ------------ | ------------
TCGA-PAAD | 143 | 37 | PACA.tcga.uuid.curated.somatic.maf | PAAD.tcga.uuid.curated.somatic.maf | Removed the two header lines (starting with '#') from the original MAF file. One sample (*TCGA-IB-7651-01A-11D-2154-08*) seems to have extremely high mutation burden.
ICGC-AU | 395 | 37 | simple_somatic_mutation.open.tsv | PACA-AU.icgc.simple_somatic_mutation.maf | Extracted rows for ICGA-AU *egrep PACA-AU simple_somatic_mutation.open.tsv > PACA-AU.icgc.simple_somatic_mutation.tsv* and added the header *head -1 simple_somatic_mutation.open.tsv > header.txt* followed by '*echo -e '0r header.txt\nw' | ed PACA-AU.icgc.simple_somatic_mutation.tsv'* and then converted to MAF file using *[icgcMutationToMAF.R](https://github.com/umccr/MAF-summary/tree/master/icgcMutationToMAF.R)* script
ICGC-AU (additional) | 25 | 37 | DCC17_PDAC_Not_in_DCC_maf.xlsx | DCC17_PDAC_Not_in_DCC.maf | Changed the *Matched_Tumour_Sample_Barcode* to *Tumor_Sample_Barcode* to be compatible with R *maftools*
ICGC-CA | 336 | 37 | simple_somatic_mutation.open.tsv | PACA-CA.icgc.simple_somatic_mutation.maf | Extracted rows for ICGA-CA *egrep PACA-CA simple_somatic_mutation.open.tsv > PACA-CA.icgc.simple_somatic_mutation.tsv* and added the header *head -1 simple_somatic_mutation.open.tsv > header.txt* followed by *echo -e '0r header.txt\nw' | ed PACA-CA.icgc.simple_somatic_mutation.tsv* and then converted to MAF file using *[icgcMutationToMAF.R](https://github.com/umccr/MAF-summary/tree/master/icgcMutationToMAF.R)* script
Witkiewicz ([PMID: 25855536](https://www.ncbi.nlm.nih.gov/pubmed/25855536)) | 109 | 37 | UTSW-MAF.xlsx | To be generated | WXS data to re-analyse, as only coding mutations were reported (no silent mutation)
Combined | 1008 | 37 | To be generated | - | Exclude TCGA-PAAD sample TCGA-IB-7651-01A-11D-2154-08 with the extremely high mutation burden?
<br />


## Scripts

Script | Description | Packages
------------ | ------------ | ------------
*[icgcMutationToMAF.R](https://github.com/umccr/MAF-summary/tree/master/icgcMutationToMAF.R)* | Converts ICGC [Simple Somatic Mutation Format](http://docs.icgc.org/submission/guide/icgc-simple-somatic-mutation-format/) file to [MAF](https://software.broadinstitute.org/software/igv/MutationAnnotationFormat) file | *[maftools](https://www.bioconductor.org/packages/devel/bioc/vignettes/maftools/inst/doc/maftools.html)*
*[summariseMAFs.R](https://github.com/umccr/MAF-summary/tree/master/summariseMAFs.R)* | Summarises and visualises multiple [MAF](https://software.broadinstitute.org/software/igv/MutationAnnotationFormat) files | *[maftools](https://www.bioconductor.org/packages/devel/bioc/vignettes/maftools/inst/doc/maftools.html)* <br> *[xlsx](https://cran.r-project.org/web/packages/xlsx/xlsx.pdf)*
<br />


## Converting ICGC Simple Somatic Mutation Format to MAF

The publicly available ICGC mutation data is stored in [Simple Somatic Mutation Format](http://docs.icgc.org/submission/guide/icgc-simple-somatic-mutation-format/) file, which is similar to MAF format in its structure, but the field names and classification of variants are different. The *[icgcMutationToMAF.R](https://github.com/umccr/MAF-summary/tree/master/icgcMutationToMAF.R)* script implements *icgcSimpleMutationToMAF* function in *[maftools](https://www.bioconductor.org/packages/devel/bioc/vignettes/maftools/inst/doc/maftools.html)* R package to convert ICGC [Simple Somatic Mutation Format](http://docs.icgc.org/submission/guide/icgc-simple-somatic-mutation-format/) to MAF.


**Script**: *[icgcMutationToMAF.R](https://github.com/umccr/MAF-summary/tree/master/icgcMutationToMAF.R)*

Argument no. | Description
------------ | ------------
1 | ICGC Simple Somatic Mutation Format file to be converted
2 | Output file name
<br />

Command line use example:

```
R --file=./icgcMutationToMAF.R --args "../data/PACA-AU.icgc.simple_somatic_mutation.tsv" "../data/PACA-AU.icgc.simple_somatic_mutation.maf"
```
<br>

This will convert the *../data/PACA-AU.icgc.simple_somatic_mutation.tsv* [Simple Somatic Mutation Format](http://docs.icgc.org/submission/guide/icgc-simple-somatic-mutation-format/) file into [Mutation Annotation Format](https://software.broadinstitute.org/software/igv/MutationAnnotationFormat) as output it as *../data/PACA-AU.icgc.simple_somatic_mutation.maf*.


## Summarising and visualising multiple MAF files

To summarise multiple MAF files run the *[summariseMAFs.R](https://github.com/umccr/MAF-summary/tree/master/summariseMAFs.R)* script. It will generate set of plots and excel spreadsheets summarising each MAF file.


**Script**: *[summariseMAFs.R](https://github.com/umccr/MAF-summary/tree/master/summariseMAFs.R)*

Argument no. | Description
------------ | ------------
1 | Directory with MAF files
2 | List of MAF files to be processed. Each file name is expected to be separated by comma
3 | Desired names of each cohort. The names are expected to be in the same order as provided MAF files
<br />

####   NOTE: Each MAF file must contain the "Tumor_Sample_Barcode" column.

Command line use example:

```
R --file=./summariseMAFs.R --args "../data" "PAAD.tcga.uuid.curated.somatic.maf, PACA-AU.icgc.simple_somatic_mutation.maf, DCC17_PDAC_Not_in_DCC.maf, PACA-CA.icgc.simple_somatic_mutation.maf" "TCGA-PAAD, ICGC-PACA-AU, ICGC-PACA-AU-additional, ICGC-PACA-CA"
```
<br>

This will generate the following output tables and plots:

Output file | Component | Description
------------ | ------------| -----------
MAF_summary.xlsx | - | Excel spreadsheet with basic information about each MAF file (NCBI build, number fo samples and genes, number of different mutation types) presented in a separate tab
MAF_sample_summary.xlsx | - | Excel spreadsheet with per-sample information about number of different types of mutations. The summary is provided for each cohort in a separate tab
MAF_gene_summary.xlsx | - | Excel spreadsheet with per-gene information about number of different types of mutations as well as mutated samples. The summary is provided for each cohort in a separate tab
MAF_fields.xlsx | - | Excel spreadsheet listing all fields (columns) in each MAF file presented in a separate tabs
MAF_summary_titv.xlsx | [*cohort*] (fraction) | Excel tab containing information about the fraction of each of the six different conversions (transitions and transversions) in each sample. The information for each cohort is provided in separate tabs
MAF_summary_titv.xlsx | [*cohort*] (count) | Excel tab containing information about the count of each of the six different conversions (transitions and transversions) in each sample. The information for each cohort is provided in separate tabs
MAF_summary_titv.xlsx | [*cohort*] (TiTv) | Excel tab containing information the fraction of transitions and transversions in each sample. The information for each cohort is provided in separate tabs
MAF_summary_[*cohort*].pdf | MAF summary | Displays number of variants in each sample as a stacked bar-plot and variant types as a box-plot summarised by *Variant_Classification* field
... | Oncoplot | A heat-map-like plot illustrating different types of mutations observed across all samples for the 10 most frequently mutated genes. The side and top bar-plots represent the frequency of mutations in each genes and in each sample, respectively.
... | Transition and transversions distribution | Contains a box-plot showing overall distribution of six different conversions and a stacked bar-plot showing fraction of conversions in each sample.
... | Compare mutation load against TCGA cohorts | Displays the observed mutation load of queried cohort among distribution of variants compiled from over 10,000 WXS samples across 33 TCGA landmark cohorts.
<br />


#### Example plots generated for MAF file from the ICGC PACA-CA cohort

<br />
<img src="Figures/MAF_summary_ICGC-PACA-CA.jpg" width="50%">

>MAF summary

<br />
<br />

* Oncoplot
<img src="Figures/Oncoplot_ICGC-PACA-CA.jpg" width="50%">
<br />
<br />

* Transition and transversions distribution
<img src="Figures/Transition_and_transversions_ICGC-PACA-CA.jpg" width="50%">
<br />
<br />

* Compare mutation load against TCGA cohorts
<img src="Figures/Compare_against_TCGA_cohorts_ICGC-PACA-CA.jpg" width="50%">
<br />
<br />
