## Compress and Index
```
bgzip body_size.vcf
bcftools index body_size.vcf.gz
```



## Defining Groups
```
nano group1.text
nano group2.text
```
Copy paste SRR107 .sorted.bam files into group 1
Copy paste SRR318 .sorted.bam files into group 2

```
bcftools view -S group1.text body_size.vcf.gz -Oz -o group1.vcf.gz
bcftools view -S group2.text body_size.vcf.gz -Oz -o group2.vcf.gz
```



## Filter VCF to biallelic SNPs
```
bcftools view -m2 -M2 -v snps group1.vcf.gz -Oz -o group1_biallelic.vcf.gz
bcftools view -m2 -M2 -v snps group2.vcf.gz -Oz -o group2_biallelic.vcf.gz
```
-O or -Oz = output format, -o = format
-M2 = max # of alleles, -m = min # of alleles
-V = types snp



## Calculate Allele Frequency
```
bcftools +fill-tags group1_biallelic.vcf.gz -Oz -o group1_af.vcf.gz -- -t AF
bcftools +fill-tags group2_biallelic.vcf.gz -Oz -o group2_af.vcf.gz -- -t AF
```
Adds allele frequency tag



## Pull out CHROM, POS, and AF
```
bcftools query -f '%CHROM\t%POS\t%INFO/AF\n' group1_af.vcf.gz > group1_af.tsv
bcftools query -f '%CHROM\t%POS\t%INFO/AF\n' group2_af.vcf.gz > group2_af.tsv
```
Formats each file to only have chromosome, position, and allele frequency to make it easier to input into Plot R
\n is a line break
\t is a tab

You can view the file by typing nano group1_af.tsv



## Merge, Compute the Difference, and Plot in R
```
R
g1 <- read.table("group1_af.tsv", col.names = c("CHROM", "POS", "AF1"))
g2 <- read.table("group2_af.tsv", col.names = c("CHROM", "POS", "AF2"))
```
c is for R to read a list
Files are renamed to g1 and g2. Type g1 or g2 to read the file


```
merged <- merge(g1, g2, by = c("CHROM", "POS"))
```
Merged the g1 and g2 into 1 file named merged. Do NOT merge the allele frequencies
You should have only 1 CHROM and 1 POS that match, and the allele frequencies of group 1 and group 2 should still be separate
You can call or create a column with $. merged $CHROM will show you all lists of chromosome within the file merged


```
merged <- na.omit(merged)
merged$AF_diff <- merged$AF1 - merged$AF2
```
Sorts the allele frequencies to show the differences between the two. You can view the differences by typing merged$AF_diff


```
pdf('merged.pdf')
plot(merged$POS, merged$AF_diff,
pch = 19, col = "steelblue",
xlab = "Position in gene", ylab = "Allele frequency difference (Group1 - Group2)",
main = "Allele frequency difference along bbc")
abline(h = 0, lty = 2, col = "grey40")
dev.off()
```

 Will look like
>pdf
>plot
+ pch
+ xlab
+  main
>abline
>dev.off
null device

 This creates a pdf of the graph that you want to show to represent your allele frequencies
Plot tells what data you want to plot
xlab and ylab is labeling your x and y values



In a SEPARATE command window where you are NOT logged into storehouse
```
scp -r visitor@134.129.113.23:/storehouse/visitor/table_3/pigmentation/merged.pdf .
```
 This will download the pdf to your computer. Mind that the period at the end has a space separating it from pdf
 Try to open it by typing open .
 Again, mind the . having a space away from open
 If it can not open, search merged.pdf in your file explorer
 When opening, it should show you your graph in google chrome
