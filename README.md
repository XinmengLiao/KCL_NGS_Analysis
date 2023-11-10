### NGS WORKFLOW (2023-11-10 updated)

## 0. Download files

## Reference genome

https://hgdownload.soe.ucsc.edu/goldenPath/hg38/bigZips/hg38.fa.gz

(Reference genome for Chromosome 1 :
https://www.ncbi.nlm.nih.gov/nuccore/CM000663.2/)


## Known sites files for BQSR from GATK resource bundle

https://storage.googleapis.com/genomics-public-data/resources/broad/hg38/v0/Homo_sapiens_assembly38.dbsnp138.vcf

https://storage.googleapis.com/genomics-public-data/resources/broad/hg38/v0/Homo_sapiens_assembly38.dbsnp138.vcf.idx


##### directories

ref="/Users/xinmengliao/Documents/Courses/KCL_NGSworkshop2023/reference_genome.fa"
known_sites="/Users/xinmengliao/Documents/Courses/KCL_NGSworkshop2023/Homo_sapiens_assembly38.dbsnp138.vcf"
reads="/Users/xinmengliao/Documents/Courses/KCL_NGSworkshop2023/reads"
results="/Users/xinmengliao/Documents/Courses/KCL_NGSworkshop2023/results"
var='/Users/xinmengliao/Documents/Courses/KCL_NGSworkshop2023/variants'
tools='/Users/xinmengliao/Documents/Courses/KCL_NGSworkshop2023/software'

##---------------------------------------

## 1. FastQC check the sequneces quality

##---------------------------------------

fastqc ${reads}/short_1.fastq -o ${reads}/
fastqc ${reads}/short_2.fastq -o ${reads}/

##---------------------------

## 2.Index reference genome

##---------------------------

echo "STEP 2: Map to reference using BWA-MEM."

##### create index for reference genome

${tools}/bwa/bwa index reference_genome.fa

##### map raw reads to reference genome

${tools}/bwa/bwa mem -M ${ref} ${reads}/short_1.fastq ${reads}/short_2.fastq > ${results}/mapped_result.sam

##------------------------------------------------

## 3. SAM --> BAM, add read groups

##------------------------------------------------

echo "STEP 3: SAM --> BAM, add read groups."

##### change SAM file into BAM file, reorder BAM file

java -jar ${tools}/picard.jar SortSam \
 VALIDATION_STRINGENCY=LENIENT \
 INPUT=${results}/mapped_result.sam \
 OUTPUT=${results}/sorted_mapped_result.bam \
 SORT_ORDER=coordinate

##### Add read groups

java -Xmx16g -jar ${tools}/picard.jar AddOrReplaceReadGroups \
 INPUT=${results}/sorted_mapped_result.bam \
 OUTPUT=${results}/grouped_mapped_sorted_result.bam SORT_ORDER=coordinate \
 RGID=short-id \
 RGLB=short-lib \
 RGPL=ILLUMINA \
 RGPU=short-01 \
 RGSM=short

##------------------------------------------------

## 4. Mark duplicates

##------------------------------------------------

echo "STEP 4: Mark duplicates."

##### mark dupliates

java -jar ${tools}/picard.jar MarkDuplicates \
 INPUT=${results}/grouped_mapped_sorted_result.bam \
 OUTPUT= ${results}/marked_result.bam \
 METRICS_FILE=metrics.txt \
 VALIDATION_STRINGENCY=LENIENT

##### check the statistics of duplicated file

${tools}/samtools-1.18/samtools flagstat ${results}/marked_result.bam > ${results}/MarkDuplicate_stat.txt

##------------------------------

## 5. Recalibrating base scores

##------------------------------

echo "STEP 5: Base quality score recalibration (bqsr)."

##### build reference dictionary for PICARD

java -jar ${tools}/picard.jar CreateSequenceDictionary \
 R=${ref} \
 O=reference_genome.dict

##### build referenece index

${tools}/samtools-1.18/samtools faidx ${ref}

##### create the recalibration table, which contains the recalibrated scores for each base

${tools}/gatk-4.4.0.0/gatk BaseRecalibrator \
 --known-sites ${known_sites} \
 -R ${ref} \
 -I ${results}/marked_result.bam \
 -O ${results}/recalibrated_score.table

##### apply the recalibrated scores to the BAM file for recalibration

${tools}/gatk-4.4.0.0/gatk ApplyBQSR \
 --bqsr-recal-file ${results}/recalibrated_score.table \
 -I ${results}/marked_result.bam \
 -O ${results}/final_bqsr_result.bam \
 -R ${ref}

##------------------------------------

## 6. Common Calling variants (GATK)

##------------------------------------

echo "STEP 6: Calling Variants (GATK)."

##### call variants

${tools}/gatk-4.4.0.0/gatk HaplotypeCaller \
 -R ${ref} \
 -I ${results}/final_bqsr_result.bam \
 -O ${var}/all_variants.vcf

##### extract SNPs & INDELS

${tools}/gatk-4.4.0.0/gatk SelectVariants \
 -R ${ref} -V ${var}/all_variants.vcf \
 --select-type SNP -O ${var}/raw_snps.vcf\

${tools}/gatk-4.4.0.0/gatk SelectVariants \
 -R ${ref} \
 -V ${var}/all_variants.vcf \
 --select-type INDEL -O ${var}/raw_indels.vcf
