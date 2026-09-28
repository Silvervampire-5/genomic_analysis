## BCFTools

```
bcftools mpileup -Ou -f bbc.fasta SRR10729165.sorted.bam SRR10729166.sorted.bam SRR10729566.sorted.bam SRR10733526.sorted.bam SRR31835375.sorted.bam SRR3185473.sorted.bam SRR31834582.sorted.bam SRR31835573.sorted.bam | bcftools call -mv -Ov -o body_size.vcf

```
