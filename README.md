# nf-GATK_Exome_Preprocess

## 1. Description
Adapted from the GATK best practice guide to preprocess whole exome sequencing (WES) data

## 2. Documentation of tools and approaches used for preprocessing
#### Preprocessing:
- Mapping: http://gatkforums.broadinstitute.org/gatk/discussion/6483/how-to-map-and-clean-up-short-read-sequence-data-efficiently
- Marking Duplicates: http://gatkforums.broadinstitute.org/gatk/discussion/6747/how-to-mark-duplicates-with-markduplicates-or-markduplicateswithmatecigar
- Realignment around indels: http://gatkforums.broadinstitute.org/gatk/discussion/2800/howto-perform-local-realignment-around-indels
- Base Quality Score Recalibration: http://gatkforums.broadinstitute.org/gatk/discussion/2801/howto-recalibrate-base-quality-scores-run-bqsr

#### Qualiy control metrics:
- FastQC: http://www.bioinformatics.babraham.ac.uk/projects/fastqc/
- Picard: https://broadinstitute.github.io/picard/

#### Plotting:
- MultiQC: multiqc.info (currently using v0.9)

## 3. Preparing reference files before running workflow
See here for downloading reference and vcf files: https://software.broadinstitute.org/gatk/guide/article?id=1213

This workflow assumes that the FASTA reference file has been preprocessed and indices have been made according to:
http://gatkforums.broadinstitute.org/wdl/discussion/2798/howto-prepare-a-reference-for-use-with-bwa-and-gatk

## 4. Input parameters
```
params.infile = "fastq_input.txt"
params.targets = "SureSelect_Human_All_Exon_V4_plus_UTRs.UCSC_hg19.targets.interval_list"
params.baits = "SureSelect_XT_V4_plusUTRs_baits.UCSC_hg19.capture.interval_list"
params.gatk_jar = "/share/pkg/gatk/3.5/install/GenomeAnalysisTK.jar"
params.picard_jar = "/share/pkg/picard/2.8.0/install/lib/picard.jar"
params.ref = "ucsc.hg19.fasta"
params.gold_indels1 = "1000G_phase1.indels.hg19.sites.vcf"
params.gold_indels2 = "Mills_and_1000G_gold_standard.indels.hg19.sites.vcf"
params.dbsnp = "dbsnp_138.hg19.vcf"
params.output_dir = "outputDir"
params.infile_header = true
```

#### Input file description
The input file should be tab delimited and contain 11 columns: 
1. INDIVIDUAl_ID - The ID of the individual from which the sample was derived.
2. SAMPLE_ID - The ID of the sample. Note that more than one sample can come from the same individual (e.g. tumor/normal pair)
3. LIBRARY_ID - The ID of the DNA library. Multiple sequencing libraries can be prepared from the same sample.
4. RG_ID - Read group ID
5. PLATFORM_UNIT - Generally is the read group ID plus the library ID
6. PLATFORM - Sequencer (e.g. illumina)
7. PLATFORM_MODEL - Sequencer (e.g. HiSeq2500)
8. RUN_DATE - Date of sequencing run
9. CENTER - Location of sequencing run
10. R1 - Fastq file 1
11. R2 - Fastq file 2

For more information on how to properly form read group IDs, please see the following:
https://software.broadinstitute.org/gatk/guide/article?id=6472


#### Target and bait information for the hybrid capture protocol:
- targets: The genomic location of gene/exon boundaries that are supposed to be captured (in interval list format)
- baits: The genomic location of the actual baits/probes used for capture (in interval list format)

#### JAR files
This has been tested using GATK v3.5 and Picard Tools v2.8.0
gatk_jar: JAR of GATK (tested with v3.5)
picard_jar: JAR of Picard Tools (tested with v2.8.0)

#### Reference files:
ref: BWA Reference file in FASTA format
gold_indels1 = High quality indel vcf file for realignment around indels
gold_indels2 = High quality indel vcf file for realignment around indels
dbsnp = dbSNP vcf used in base quality score recalibration (BQSR)

#### Other workflow parameters
output_dir: Final output directory for linked files:
infile_header: Whether or not the input file has a header:

##4. File cleanup
This workflow does not currently delete the intermediate bams produced during the various steps. There after workflow completion, the follow commands will delete all unnecessary bam and indicies.

```
rm -fv work/*/*/*.unaligned.bam
rm -fv work/*/*/*.adapters_marked.bam
rm -fv work/*/*/*.aligned.bam.bam
rm -fv work/*/*/*.dedup.bam
rm -fv work/*/*/*.dedup.bai
rm -fv work/*/*/*.realign.bam
rm -fv work/*/*/*.realign.bai
```





