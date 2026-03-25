
# Variant Calling and SNP Analysis using Reference-Based Genomics Pipeline

## Overview

This project implements a complete reference-based variant calling pipeline using paired-end whole genome sequencing (WGS) data of *Escherichia coli*. The workflow includes quality control, read trimming, alignment to a reference genome, variant detection, and downstream SNP analysis.

The objective of this study is to identify high-confidence single nucleotide polymorphisms (SNPs) and analyze their distribution and mutation patterns across the genome using a reproducible bioinformatics workflow.

---

## Dataset

* **Input reads:**

  * sample_R1.fastq
  * sample_R2.fastq

* **Reference genome:**

  * GCF_000017985.1_ASM1798v1_genomic.fna

* **Organism:**
  *Escherichia coli* (REL606)

* **Genome size:**
  Approximately 4.6 Mb

---

## Pipeline Workflow

The pipeline follows a standard reference-based variant calling approach:

1. Quality Control
2. Read Trimming
3. Sequence Alignment
4. SAM/BAM Processing
5. Variant Calling
6. SNP Extraction
7. Post-processing Analysis

---

## Tools Used

| Tool             | Purpose                                              |
| ---------------- | ---------------------------------------------------- |
| FastQC           | Quality assessment of raw sequencing reads           |
| Cutadapt         | Removal of low-quality bases and short reads         |
| BWA-MEM          | Alignment of reads to reference genome               |
| SAMtools         | Conversion, sorting, and indexing of alignment files |
| BCFtools         | Variant calling and filtering                        |
| awk / cut / grep | Post-processing and data extraction                  |
| R (optional)     | Statistical analysis and visualization               |

---

## Key Commands

### Quality Control

```bash
fastqc sample_R1.fastq sample_R2.fastq
```

### Read Trimming

```bash
cutadapt -q 20 -m 30 \
-o trimmed_R1.fastq \
-p trimmed_R2.fastq \
sample_R1.fastq sample_R2.fastq
```

### Alignment

```bash
bwa mem reference.fna trimmed_R1.fastq trimmed_R2.fastq > aligned.sam
```

### File Processing

```bash
samtools view -S -b aligned.sam > aligned.bam
samtools sort aligned.bam -o aligned_sorted.bam
samtools index aligned_sorted.bam
```

### Variant Calling

```bash
bcftools mpileup -f reference.fna aligned_sorted.bam -Ov -o raw.vcf
bcftools call -mv raw.vcf -Ov -o variants.vcf
```

### SNP Extraction

```bash
bcftools view -v snps variants.vcf > snps.vcf
```

### Post-processing

```bash
cut -f1,2,4,5 high_quality_snps.vcf | grep -v "^#" > final_snps_simple.txt
awk '{print $2}' final_snps_simple.txt | sort -n > snp_positions.txt
```

---

## Results Summary

* Total reads: ~3.1 million
* Mapping rate: ~99.37%
* Properly paired reads: ~96.86%
* Total SNPs identified: 33

The SNPs were distributed across the entire genome, indicating uniform coverage. SNP density analysis revealed uneven distribution with certain genomic regions showing higher mutation frequency. Mutation type analysis demonstrated the presence of both transitions and transversions.

---


## Important Note

Due to large file sizes, raw sequencing data (FASTQ, BAM files) are not included in this repository. Only processed results and scripts are provided. The dataset can be accessed from public databases such as NCBI.

---

## Future Scope

* Functional annotation of variants
* Gene-level impact analysis
* Comparative genomics studies
* Integration with machine learning approaches
* Advanced visualization using R or Python
---

## References

* Jeong, H. et al. (2009). Genome sequences of *Escherichia coli* B strains REL606 and BL21(DE3). Journal of Molecular Biology.
* Li, H. (2013). Aligning sequence reads with BWA-MEM.
* Data Carpentry Genomics Workshop: Variant Calling


