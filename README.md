### NGS WORKFLOW

## 1. Download files

## Reference genome

`wget https://hgdownload.soe.ucsc.edu/goldenPath/hg38/bigZips/hg38.fa.gz`

(Reference genome for Chromosome 1 :
https://www.ncbi.nlm.nih.gov/nuccore/CM000663.2/)

`gunzip hg38.fa.gz`

`mv hg38.fa reference_genome.fa`


## Known sites files for BQSR from GATK resource bundle

`wget https://storage.googleapis.com/genomics-public-data/resources/broad/hg38/v0/Homo_sapiens_assembly38.dbsnp138.vcf`

`wget https://storage.googleapis.com/genomics-public-data/resources/broad/hg38/v0/Homo_sapiens_assembly38.dbsnp138.vcf.idx`

## 2. FastQC check the sequneces quality

`fastqc short_1.fastq`

`fastqc short_2.fastq`

## 3.Index reference genome

`bwa index reference_genome.fa`

## 4. Align fastq files with the referene genome

`bwa mem -M reference_genome.fa short_1.fastq short_2.fastq > result.sam`

## 5. Mark duplicates

## change SAM file into BAM file, reorder BAM file

`java -jar picard.jar SortSam VALIDATION_STRINGENCY=LENIENT INPUT=result.sam OUTPUT=sorted_file.bam SORT_ORDER=coordinate`

## mark dupliates

`java -jar picard.jar MarkDuplicates INPUT=sorted_file.bam OUTPUT= sorted_and_marked_file.bam METRICS_FILE=metrics.txt VALIDATION_STRINGENCY=LENIENT`

## check the statistics of duplicated file

`samtools flagstat sorted_and_marked_file.bam`

## 6. Realignment

## Add read groups

`java -jar picard.jar AddOrReplaceReadGroups INPUT=sorted_and_marked_file.bam OUTPUT=final_result.bam SORT_ORDER=coordinate  RGID=short-id RGLB=short-lib RGPL=ILLUMINA RGPU=short-01 RGSM=short`

## build BAM index

`java -jar picard.jar BuildBamIndex INPUT=final_result.bam`


## 7. Recalibrating base scores
## build reference dictionary for PICARD

`java -jar picard.jar CreateSequenceDictionary R=reference_genome.fa O=reference_genome.dict`

## build referenece index

`samtools faidx reference_genome.fa`

##  Recalibration

`gatk BaseRecalibrator –R reference_genome.fa -I final_result.bam –known-sites Homo_sapiens_assembly38.dbsnp138.vcf –O result_recal.table`

`gatk ApplyBQSR -I final_result.bam –R reference_genome.fa --bqsr-recal-file result_recal.table -O result_recal.bam`


## 8. Calling variants

## call variants

`gatk HaplotypeCaller -R reference_genome.fa -I result_recal.bam -O variants_result.vcf`

## check variants file

`less -S variants_result.vcf`

## extract SNPs & INDELS

`gatk SelectVariants -R reference_genome.fa -V variants_result.vcf --select-type SNP -O raw_snps.vcf`

`gatk SelectVariants -R reference_genome.fa -V variants_result.vcf --select-type INDEL -O raw_indels.vcf`
