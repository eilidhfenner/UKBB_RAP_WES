# UKBB 500k RAP processing
Scripts for the processing of WES data from ~500k individuals using the UKBB Research Analysis Platform (RAP) - a cloud based platform. All stages of this analysis were either run in Hail (through a jupyterlab session (.ipynb files)) or in R (through an R markdown session (.Rmd files)).

## Stages of processing 
The list below gives a short descriptions of each script, and further detail can be found in the jupyterlab (.ipynb) and RMarkdown (.Rmd) files. Scripts are numbered in order of processing. Each individual script includes a run down of the instance used to run it on the RAP, and the cost of the processing. The following scripts were run in order on the RAP: 
- **Stage 1:** _'01_import_pVCFs_and_genotype_QC.ipynb'_
    - Reads files from their original format in the UKBB (pVCFs) into Hail, runs genotype quality control, and writes out sample and variant level QC metrics, as well as matrix tables of the data.
    - Total cost: £1,730
- **Stage 2:** _'02_variant_QC.Rmd'_
    - Reads in variant QC metrics written out in stage 1 and cleans and writes these out to a single variant QC table containing all variants. Visualises variant level QC metrics to enable cut-offs for these to be defined. 
    - Total cost: £4
- **Stage 3:** _'03_sample_QC.Rmd'_
    - Reads in sample-level QC metrics written out in stage 1 and cleans and writes these to a single sample-level QC table, with means for different metrics for each sample from across all chromosomes. Visualises sample level QC metrics to enable cut-offs for these to be defined. 
    - Total cost: £3 
- **Stage 4:** _'04_VEP_annotations.ipynb'_
    - Reads in matrix table from stage 1 and annotates variants using VEP and LoFTEE. The anotation uses an .json config file, a copy of which is saved within this repository (_'04a_helper_file_config_plugin_details.json'_). The table of annotated variants is written out for use later in processing. 
    - Total cost: £45
- **Stage 5:** _'05_applying_QC_filters.ipynb'_ 
    - Reads in matrix table from stage 1 and applies variant QC cut-offs (determined in Stage 2), removes variants in low complexity regions (LCRs), and applies sample-QC cut-offs (determined in Stage 3). The resulting matrix table is then written out. 
    - Total cost: £33
- **Stage 6:** _'06_relatedness_filering.Rmd'_ 
    - This stage determines 1st, 2nd, and 3rd degree relations to remove to leave a maximal set of unrelated individuals using pairwise kinship co-efficients caclulated from array data, which are available for use on the RAP. The list of individuals to remove is written out. 
    - Total cost: £11
- **Stage 7:** _'07_impute_sex.ipynb'_ 
    - Reads in matrix table from the X chromosome written out in Stage 6, and filters to high-quality, non-rare variants. From these variants, impute sex and writes imputed sex metrics out. 
    - Total cost: £0.20
- **Stage 8** _'08_sex_inference.Rmd'_
    - Uses outputs from stage 7 to highlight individuals with mismatching imputed sex, phenotypic sex, and y-depth, and writes a list of IDs with concordant and discordant sex. 
    - Total cost: £0.30
- **Stage 9:** _'09_Ancestry_inference.Rmd'_ 
    - Uses UK Biobank array data to calculate PCs of ancestry, infer genetic ancestry probabilities, and derive ancestry-specific PCs. These are all used downstream to covary for population stratification. This script was run on an in-house supercomputing environment (not on the RAP) as this is where UK Biobank array data was stored at this time. Outputs of this script were uploaded to the RAP for use downstream. 
    - Total cost: free as not run on the RAP. 
- **Stage 10:** _'10_apply_QC_cutoffs_and_annotations.ipynb'_
    - Reads in matrix tables from stage 5 and applies QC filters and filters on relatedness (using outputs from stage 6). Matrix tables are then annotated with VEP annotations (from stage 4) and then writes out count-ready matrix tables. 
    - Total cost: £13
- **Stage 11:** _'11_count_qualifying_variants_per_gene.ipynb'_
    - Reads in the matrix table written in stage 10 and counts the burden of qualifying variants (you select this definition) per gene for each sample. Then writes out a table (.tsv) of this burden across all genes in a chromosome.
    - Total cost: £35
- **Stage 12:** _'12_transpose_counts.Rmd'_
    - Reads in counts from stage 11 and transposes these. Writes out transposed counts tables.
    - Total cost: £2
- **Stage 13:** _'13_Defining_gene_sets.Rmd'_
    - This script uses data from different publications to define different gene sets. It was run locally and outputs were then uploaded to the RAP. 
    - Total cost: free as not run on the RAP. 
- **Stage 14:** _'14_gene_set_counts.Rmd'_
    - Reads in transposed counts tables from 12 and adds up counts across all genes within a set. Outputs set specific counts per person for use in regression analyses. 
    - Total cost: £1
- **Stage 15:** _'15_processing_cognitive_tests.Rmd'_
    - Processes phenotype data from UKBB cognitive tests. It cleans the data and forms a g score. 
    - Total cost: £0.20 
- **Stage 16:** _'16_gene_set_regressions.Rmd'_
    - Reads in phenotypic data from stage 15, and tables of counts per gene set from stage 14. Then runs regressions on these data. 
    - Total cost: £0.20
- **Stage 17:** _'17_single_gene_counts.Rmd'_
    - Reads in transposed counts (from stage 12) for each chromosome, filters to the genes we want to do a single gene burden test in, and writes this table out out for use in single gene regressions. 
    - Total cost: £2
- **Stage 18:** _'18_single_gene_regressions.Rmd'_
    - Reads in phenotypic data from stage 15, and tables of counts from from stage 17. Then runs regressions on these data. 
    - Total cost: £2.20 
- **Stage 19:** _'19_combined_analysis.Rmd'_
    - This script runs a combined analysis of RCVs, CNVs and PRS.
    - Total cost: £0.40 
- **Stage 20:** _'20_plot_results.Rmd'_
    - All results of regressions were downloaded from the RAP and this script then investigated and plotted results locally. 

    
    
    
