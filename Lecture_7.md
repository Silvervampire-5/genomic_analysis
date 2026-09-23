## Trim adapters and quality check reads
```
for i in *.lite.1_1.fastq
do
OUT=${i%.lite.1_1.fastq}
fastp -i $OUT.lite.1_1.fastq -I 1$OUT.lite.1_2.fastq -o $OUT.lite.trim.1_1.fastq -O $OUT.lite.trim.1_2.fastq
```

## BWA Index the Reference

```
bwa index bbc.fasta

```
## BWA Mem
```
for i in *.lite.1_1.fastq
do
OUT=${i%.lite.1_1.fastq}
bwa mem -t 10 bbc.fasta $OUT.lite.1_1.fastq $OUT.lite.1_2.fastq > $OUT.sam
done
```

## Samtools View
```
for i in *.sam
do
OUT=${i%.sam}
samtools view -b $OUT.sam -o $OUT.bam
samtools sort $OUT.bam -o $OUT.sorted.bam
done
```

