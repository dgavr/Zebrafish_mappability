# Mappability tracks for danRer7 and danRer10

Genomes contain a large number of repeated elements and regions bearing similarity. When mapping genomics reads it is important to bear in mind whether reads fall into these regionss and possibly ignore such regions or to determine the number of multimapping reads to allow. 

Here is a resource using two methods to generate such "mappabilty" tracks using: 

(1) a python script and bowtie mapper. 
(2) Using [GEM](https://wiki.bits.vib.be/index.php/Create_a_mappability_track) package

As zebrafish genomes currently available lack such a resource, possibly useful danRer10 and danRer7 mappability tracks are proposed. Finished tracks are available as a hub in UCSC genome browser : 

**UCSC genome browser hubs for danRer7/Zv9 Mappability**: 

```{r eval = FALSE}
http://userweb.molbiol.ox.ac.uk/public/dariag/mappability/hub_qd_zv9.txt
http://userweb.molbiol.ox.ac.uk/public/dariag/mappability/hub_gem_zv9.txt
```

**UCSC genome browser hubs for GRCz10/danRer10 Mappability**: 

```{r eval = FALSE}
http://userweb.molbiol.ox.ac.uk/public/dariag/mappability/hub_qd_zv10.txt
http://userweb.molbiol.ox.ac.uk/public/dariag/mappability/hub_gem_zv10.txt
```

This resource was generated as part of the FoxD3 project carried out in the laboratory of T. Sauka-Spengler at the Weatherall Institute of Molecular Medicine at the University of Oxford, 
in the UK. 

Preliminary manuscript for this project is available on bioRxiv: https://www.biorxiv.org/content/biorxiv/early/2017/11/22/213611.full.pdf

For additional information on project please see [tsslab/foxd3](https://github.com/tsslab/foxd3) github repository.  

#### Approach I: Mapping-based alignability 

1. Get fasta sequence of genome.

* For Zv9/danRer7 from UCSC genome browser [here](http://hgdownload.soe.ucsc.edu/goldenPath/danRer7/bigZips/) 
* For GRCz10/danRer10 from UCSC genome browser[here](http://hgdownload.soe.ucsc.edu/goldenPath/danRer10/bigZips/)

2. Make read set covering whole genome using genomeFasta2reads.py script

&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;To use :

```{r eval = FALSE}
genomeFasta2reads.py <genome.fa> <desired_read_length>
```

&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;This will generates fastq-formatted "reads" with a phred quality score H of desired length with a step of 1. 

&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Example: 

```{r eval = FALSE}
@synthetic_read-danRer10:chr1:0:40
GATCTTAAACATTTATTCCCCCTGCAAACATTTTCAATCA
+
HHHHHHHHHHHHHHHHHHHHHHHHHHHHHHHHHHHHHHHH
@synthetic_read-danRer10:chr1:1:41
ATCTTAAACATTTATTCCCCCTGCAAACATTTTCAATCAT
+
HHHHHHHHHHHHHHHHHHHHHHHHHHHHHHHHHHHHHHHH
@synthetic_read-danRer10:chr1:2:42
TCTTAAACATTTATTCCCCCTGCAAACATTTTCAATCATT
+
HHHHHHHHHHHHHHHHHHHHHHHHHHHHHHHHHHHHHHHH
```

&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Links to the fastq.gz files:

* danRer7 [40bp](http://userweb.molbiol.ox.ac.uk:/public/dariag/damir/danRer7_40bp_reads.fastq.gz)  [50bp](http://userweb.molbiol.ox.ac.uk:/public/dariag/damir/danRer7_50bp_reads.fastq.gz)  [75bp](http://userweb.molbiol.ox.ac.uk:/public/dariag/damir/danRer7_75bp_reads.fastq.gz)   [100bp](http://userweb.molbiol.ox.ac.uk:/public/dariag/damir/danRer7_100bp_reads.fastq.gz)   [250bp](http://userweb.molbiol.ox.ac.uk:/public/dariag/damir/danRer7_250bp_reads.fastq.gz) [all above](http://userweb.molbiol.ox.ac.uk:/public/dariag/damir/danRer7_reads.tar.gz)

* danRer10 [40bp](http://userweb.molbiol.ox.ac.uk:/public/dariag/damir/danRer10_40bp_reads.fastq.gz)  [50bp](http://userweb.molbiol.ox.ac.uk:/public/dariag/damir/danRer10_50bp_reads.fastq.gz)  [75bp](http://userweb.molbiol.ox.ac.uk:/public/dariag/damir/danRer10_75bp_reads.fastq.gz)   [100bp](http://userweb.molbiol.ox.ac.uk:/public/dariag/damir/danRer10_100bp_reads.fastq.gz)   [250bp](http://userweb.molbiol.ox.ac.uk:/public/dariag/damir/danRer10_250bp_reads.fastq.gz) [all above](http://userweb.molbiol.ox.ac.uk:/public/dariag/damir/danRer10_reads.tar.gz)

3. Map fastqs back to genome:

&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Download [bowtie v.1.1.0 index for danRer7](http://userweb.molbiol.ox.ac.uk:/public/dariag/damir/danRer7_bowtie1_indices.tar.gz) 

&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Download [bowtie v.1.1.0 index for danRer10](http://userweb.molbiol.ox.ac.uk:/public/dariag/damir/danRer10_bowtie1_indices.tar.gz)


&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Map "synthetic" fastqs of different back to genome using bowtie(v.1.1.0.) using -m parameters. m1 

```{r eval = FALSE}

R1=danRer7_40bp_reads.fastq

bowtie -S -p 24 -m 1 danRer7 $R1 --chunkmb 500 > | samtools view -bS - > zv7_on_zv7_m1_40bp_b1.bam 
bowtie -S -p 24 -m 2 danRer7 $R1 --chunkmb 500  | samtools view -bS - > zv7_on_zv7_m2_40bp_b1.bam 
bowtie -S -p 24 -m 3 danRer7 $R1 --chunkmb 500  | samtools view -bS - > zv7_on_zv7_m3_40bp_b1.bam 
```

&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;If in a hurry, split fastqs into multiple smaller fastqs and map each individually and then merge using bamtools merge. 

4. Use bedtools genomeCoverageBed to generate bedgraph files.  To visualise make bigwigs using bedGraphToBigWig from UCSC genome browser.

&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Links to the bigwig files:

*  danRer7 40bp: [-m1](http://userweb.molbiol.ox.ac.uk:/public/dariag/damir/zv9_on_zv9_m1_40bp_b1.bw)  [-m2](http://userweb.molbiol.ox.ac.uk:/public/dariag/damir/bmerged_zv7_on_zv7_m2_40bp_b1.bg.bw) [-m3](http://userweb.molbiol.ox.ac.uk:/public/dariag/damir/bmerged_zv7_on_zv7_m3_40bp_b1.bg.bw)
*  danRer7 50bp: [-m1](http://userweb.molbiol.ox.ac.uk:/public/dariag/damir/bmerged_zv7_on_zv7_m1_50bp_b1.bg.bw)  [-m2](http://userweb.molbiol.ox.ac.uk:/public/dariag/damir/bmerged_zv7_on_zv7_m2_50bp_b1.bg.bw) [-m3](http://userweb.molbiol.ox.ac.uk:/public/dariag/damir/bmerged_zv7_on_zv7_m3_50bp_b1.bg.bw)
*  danRer7 75bp: [-m1](http://userweb.molbiol.ox.ac.uk:/public/dariag/damir/bmerged_zv7_on_zv7_m1_75bp_b1.bg.bw)  [-m2](http://userweb.molbiol.ox.ac.uk:/public/dariag/damir/bmerged_zv7_on_zv7_m2_75bp_b1.bg.bw) [-m3](http://userweb.molbiol.ox.ac.uk:/public/dariag/damir/bmerged_zv7_on_zv7_m3_75bp_b1.bg.bw)
*  danRer7 100bp: [-m1](http://userweb.molbiol.ox.ac.uk:/public/dariag/damir/bmerged_zv7_on_zv7_m1_100bp_b1.bg.bw) [-m2](http://userweb.molbiol.ox.ac.uk:/public/dariag/damir/bmerged_zv7_on_zv7_m2_100bp_b1.bg.bw) [-m3](http://userweb.molbiol.ox.ac.uk:/public/dariag/damir/bmerged_zv7_on_zv7_m3_100bp_b1.bg.bw)

*  danRer10 40bp: [-m1](http://userweb.molbiol.ox.ac.uk:/public/dariag/damir/bmerged_zv10_on_zv10_m1_40bp_b1.bg.bw)  [-m2](http://userweb.molbiol.ox.ac.uk:/public/dariag/damir/bmerged_zv10_on_zv10_m2_40bp_b1.bg.bw) [-m3](http://userweb.molbiol.ox.ac.uk:/public/dariag/damir/bmerged_zv10_on_zv10_m3_40bp_b1.bg.bw)
*  danRer10 50bp:  [-m1](http://userweb.molbiol.ox.ac.uk:/public/dariag/damir/bmerged_zv10_on_zv10_m1_50bp_b1.bg.bw)  [-m2](http://userweb.molbiol.ox.ac.uk:/public/dariag/damir/bmerged_zv10_on_zv10_m2_50bp_b1.bg.bw) [-m3](http://userweb.molbiol.ox.ac.uk:/public/dariag/damir/bmerged_zv10_on_zv10_m3_50bp_b1.bg.bw)
*  danRer10 75bp:  [-m1](http://userweb.molbiol.ox.ac.uk:/public/dariag/damir/bmerged_zv10_on_zv10_m1_75bp_b1.bg.bw)  [-m2](http://userweb.molbiol.ox.ac.uk:/public/dariag/damir/bmerged_zv10_on_zv10_m2_75bp_b1.bg.bw) [-m3](http://userweb.molbiol.ox.ac.uk:/public/dariag/damir/bmerged_zv10_on_zv10_m3_75bp_b1.bg.bw)
*  danRer10 100bp: [-m1](http://userweb.molbiol.ox.ac.uk:/public/dariag/damir/bmerged_zv10_on_zv10_m1_100bp_b1.bg.bw)  [-m2](http://userweb.molbiol.ox.ac.uk:/public/dariag/damir/bmerged_zv10_on_zv10_m2_100bp_b1.bg.bw) [-m3](http://userweb.molbiol.ox.ac.uk:/public/dariag/damir/bmerged_zv10_on_zv10_m3_100bp_b1.bg.bw)


5. Parse bedgraph file to select regions that have a signal corresponding to length of read. Then use bedtools complement to get the complement (all regions that do not have that signal). 

```{r eval = FALSE}

awk '$4 == 40' bmerged_zv7_on_zv7_m1_40bp_b1.bg > bmerged_zv7_on_zv7_m1_40bp_b1_eq.bed
sort -k1,1 -k2,2n bmerged_zv7_on_zv7_m1_40bp_b1_eq.bed > bmerged_zv7_on_zv7_m1_40bp_b1_eq.sort.bed
bedtools complement -i bmerged_zv7_on_zv7_m1_40bp_b1_eq.sort.bed -g danRer7.chrom.sizes > com_bmerged_zv7_on_zv7_m1_40bp_b1_eq.sort.bed

awk '$4 == 40' bmerged_zv10_on_zv10_m1_40bp_b1.bg > bmerged_zv10_on_zv10_m1_40bp_b1_eq.bed
sort -k1,1 -k2,2n bmerged_zv10_on_zv10_m1_40bp_b1_eq.bed > bmerged_zv10_on_zv10_m1_40bp_b1_eq.sort.bed
bedtools complement -i bmerged_zv10_on_zv10_m1_40bp_b1_eq.sort.bed -g danRer10.chrom.sizes > com_bmerged_zv10_on_zv10_m1_40bp_b1_eq.sort.bed
```

&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Links to the bed.gz files to regions to exclude: 

*  danRer7 40bp: [-m1](http://userweb.molbiol.ox.ac.uk:/public/dariag/damir/com_bmerged_zv7_on_zv7_m1_40bp_b1_eq.sort.bed.gz)  [-m2](http://userweb.molbiol.ox.ac.uk:/public/dariag/damir/com_bmerged_zv7_on_zv7_m2_40bp_b1_eq.sort.bed.gz) [-m3](http://userweb.molbiol.ox.ac.uk:/public/dariag/damir/com_bmerged_zv7_on_zv7_m3_40bp_b1_eq.sort.bed.gz)
*  danRer7 50bp: [-m1](http://userweb.molbiol.ox.ac.uk:/public/dariag/damir/com_bmerged_zv7_on_zv7_m1_50bp_b1_eq.sort.bed.gz)  [-m2](http://userweb.molbiol.ox.ac.uk:/public/dariag/damir/com_bmerged_zv7_on_zv7_m2_50bp_b1_eq.sort.bed.gz) [-m3](http://userweb.molbiol.ox.ac.uk:/public/dariag/damir/com_bmerged_zv7_on_zv7_m3_50bp_b1_eq.sort.bed.gz)
*  danRer7 75bp: [-m1](http://userweb.molbiol.ox.ac.uk:/public/dariag/damir/com_bmerged_zv7_on_zv7_m1_75bp_b1_eq.sort.bed.gz)  [-m2](http://userweb.molbiol.ox.ac.uk:/public/dariag/damir/com_bmerged_zv7_on_zv7_m2_75bp_b1_eq.sort.bed.gz) [-m3](http://userweb.molbiol.ox.ac.uk:/public/dariag/damir/com_bmerged_zv7_on_zv7_m3_75bp_b1_eq.sort.bed.gz)
*  danRer7 100bp: [-m1](http://userweb.molbiol.ox.ac.uk:/public/dariag/damir/com_bmerged_zv7_on_zv7_m1_100bp_b1_eq.sort.bed.gz)  [-m2](http://userweb.molbiol.ox.ac.uk:/public/dariag/damir/com_bmerged_zv7_on_zv7_m2_100bp_b1_eq.sort.bed.gz) [-m3](http://userweb.molbiol.ox.ac.uk:/public/dariag/damir/com_bmerged_zv7_on_zv7_m3_100bp_b1_eq.sort.bed.gz)


*  danRer10 40bp: [-m1](http://userweb.molbiol.ox.ac.uk:/public/dariag/damir/com_bmerged_zv10_on_zv10_m1_40bp_b1_eq.bed.gz)  [-m2](http://userweb.molbiol.ox.ac.uk:/public/dariag/damir/com_bmerged_zv10_on_zv10_m2_40bp_b1_eq.bed.gz) [-m3](http://userweb.molbiol.ox.ac.uk:/public/dariag/damir/com_bmerged_zv10_on_zv10_m3_40bp_b1_eq.bed.gz)
*  danRer10 50bp: [-m1](http://userweb.molbiol.ox.ac.uk:/public/dariag/damir/com_bmerged_zv10_on_zv10_m1_50bp_b1_eq.bed.gz)  [-m2](http://userweb.molbiol.ox.ac.uk:/public/dariag/damir/com_bmerged_zv10_on_zv10_m2_50bp_b1_eq.bed.gz) [-m3](http://userweb.molbiol.ox.ac.uk:/public/dariag/damir/com_bmerged_zv10_on_zv10_m3_50bp_b1_eq.bed.gz)
*  danRer10 75bp: [-m1](http://userweb.molbiol.ox.ac.uk:/public/dariag/damir/com_bmerged_zv10_on_zv10_m1_75bp_b1_eq.sort.bed.gz)  [-m2](http://userweb.molbiol.ox.ac.uk:/public/dariag/damir/com_bmerged_zv10_on_zv10_m2_75bp_b1_eq.sort.bed.gz) [-m3](http://userweb.molbiol.ox.ac.uk:/public/dariag/damir/com_bmerged_zv10_on_zv10_m3_75bp_b1_eq.sort.bed.gz)
*  danRer10 100bp: [-m1](http://userweb.molbiol.ox.ac.uk:/public/dariag/damir/com_bmerged_zv10_on_zv10_m1_100bp_b1_eq.sort.bed.gz)  [-m2](http://userweb.molbiol.ox.ac.uk:/public/dariag/damir/com_bmerged_zv10_on_zv10_m2_100bp_b1_eq.sort.bed.gz) [-m3](http://userweb.molbiol.ox.ac.uk:/public/dariag/damir/com_bmerged_zv10_on_zv10_m3_100bp_b1_eq.sort.bed.gz)

#### Approach II: GEM

1. Create Genome index file 

```{r eval = FALSE}
gem-indexer -i danRer7.fa -o danRer7_index
```

[danRer7 .gem file](http://userweb.molbiol.ox.ac.uk:/public/dariag/damir/danRer7_index.gem)
[danRer10 .gem file](http://userweb.molbiol.ox.ac.uk:/public/dariag/damir/danRer10_index.gem)

2. Generate .mappability file 

```{r eval = FALSE}
gem-mappability -I danRer7_index.gem -l 50 -o danRer7_40 -T 24
```

&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Links to .mappability files: 

* danRer7 [40bp](http://userweb.molbiol.ox.ac.uk:/public/dariag/damir/danRer7_40.mappability) [50bp](http://userweb.molbiol.ox.ac.uk:/public/dariag/damir/danRer7_50.mappability) [75bp](http://userweb.molbiol.ox.ac.uk:/public/dariag/damir/danRer7_75.mappability) [100bp](http://userweb.molbiol.ox.ac.uk:/public/dariag/damir/danRer7_100.mappability)  


* danRer10 [40bp](http://userweb.molbiol.ox.ac.uk:/public/dariag/damir/danRer10_40.mappability) [50bp](http://userweb.molbiol.ox.ac.uk:/public/dariag/damir/danRer10_50.mappability) [75bp](http://userweb.molbiol.ox.ac.uk:/public/dariag/damir/danRer10_75.mappability) [100bp](http://userweb.molbiol.ox.ac.uk:/public/dariag/damir/danRer10_100.mappability)  

2. Generate .wig file and bigWig files 

&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Links to GEM bigwig files: 

*  danRer7 [40bp](http://userweb.molbiol.ox.ac.uk:/public/dariag/damir/danRer7_40.bw) [50bp](http://userweb.molbiol.ox.ac.uk:/public/dariag/damir/danRer7_50.bw) [75bp](http://userweb.molbiol.ox.ac.uk:/public/dariag/damir/danRer7_75.bw) [100bp](http://userweb.molbiol.ox.ac.uk:/public/dariag/damir/danRer7_100.bw) 


*  danRer10 [40bp](http://userweb.molbiol.ox.ac.uk:/public/dariag/damir/danRer10_40.bw) [50bp](http://userweb.molbiol.ox.ac.uk:/public/dariag/damir/danRer10_50.bw) [75bp](http://userweb.molbiol.ox.ac.uk:/public/dariag/damir/danRer10_75.bw) [100bp](http://userweb.molbiol.ox.ac.uk:/public/dariag/damir/danRer10_100.bw) 

**GC content**

[GC content bigwig file for danRer7](http://userweb.molbiol.ox.ac.uk:/public/dariag/damir/danRer7.gc5Base.bw)  
[GC content bigwig file for danRer10](http://userweb.molbiol.ox.ac.uk:/public/dariag/damir/danRer10.gc5Base.bw)  

