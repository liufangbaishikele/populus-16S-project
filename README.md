# populus-16S-project

#### Download raw-data from SRA database

Sequence data

```
fastq-dump –split-files SRR3732212 SRR3732224 SRR3732232 SRR3732240 SRR3732248 SRR3732257 SRR3732266 SRR3732268 SRR3732269 SRR3732270 SRR3732271 SRR3732273
```
Metadata downloaded from [SRA-RunInfo Table link] (https://www.ncbi.nlm.nih.gov/Traces/study/WebEnv=NCID_1_2922414_130.14.18.97_5555_1478249302_1408550304_0MetA0_S_HStore&query_key=4)

####sequence preprocessing using *cutadapt*-- trim off forward primers and reverse primers as well as the random sequences ahead the primer

These random sequences ahead forward primers and behind reverse primers are introduced during library preparation, which described in [Practical innovations for high-throughput amplicon sequencing] (http://www.nature.com/nmeth/journal/v10/n10/abs/nmeth.2634.html)

```
conda install cutadapt -y 
*Forward reads
for read1 in SRR3732212_1.fastq SRR3732224_1.fastq SRR3732232_1.fastq SRR3732240_1.fastq SRR3732248_1.fastq SRR3732257_1.fastq SRR3732266_1.fastq SRR3732268_1.fastq SRR3732269_1.fastq SRR3732270_1.fastq SRR3732271_1.fastq SRR3732273_1.fastq
do
        cutadapt -g GTGCCAGCMGCCGCGGTAA  --error-rate=0.1 --overlap=19 -o trimmed_$read1 $read1
        done
*Reverse reads

for read2 in SRR3732212_2.fastq SRR3732224_2.fastq SRR3732232_2.fastq SRR3732240_2.fastq SRR3732248_2.fastq SRR3732257_2.fastq SRR3732266_2.fastq SRR3732268_2.fastq SRR3732269_2.fastq SRR3732270_2.fastq SRR3732271_2.fastq SRR3732273_2.fastq

do
        cutadapt -g GGACTACHVGGGTWTCTAAT --overlap=19 --error-rate=0.1 -o trimmed_$read2 $read2
        done
```
####sequence processing--[MiSeq-SOP] (www.mothur.org/wiki/MiSeq_SOP)

Here is the treatment table 
```
Non_Rhizo_SRX1901989    trimmed_SRR3732212_1.fastq      trimmed_SRR3732212_2.fastq
Non_Rhizo_SRX1901990    trimmed_SRR3732224_1.fastq      trimmed_SRR3732224_2.fastq
Non_Rhizo_SRX1901991    trimmed_SRR3732232_1.fastq      trimmed_SRR3732232_2.fastq
Non_Endo_SRX1901986     trimmed_SRR3732240_1.fastq      trimmed_SRR3732240_2.fastq
Non_Endo_SRX1901987     trimmed_SRR3732248_1.fastq      trimmed_SRR3732248_2.fastq
Non_Endo_SRX1901988     trimmed_SRR3732257_1.fastq      trimmed_SRR3732257_2.fastq
Enrich_Rhizo_SRX1901983 trimmed_SRR3732266_1.fastq      trimmed_SRR3732266_2.fastq
Enrich_Rhizo_SRX1901984 trimmed_SRR3732268_1.fastq      trimmed_SRR3732268_2.fastq
Enrich_Rhizo_SRX1901985 trimmed_SRR3732269_1.fastq      trimmed_SRR3732269_2.fastq
Enrich_Endo_SRX1901980  trimmed_SRR3732270_1.fastq      trimmed_SRR3732270_2.fastq
Enrich_Endo_SRX1901981  trimmed_SRR3732271_1.fastq      trimmed_SRR3732271_2.fastq
Enrich_Endo_SRX1901982  trimmed_SRR3732273_1.fastq      trimmed_SRR3732273_2.fastq
```

Sequence processing command 

* make contigs
* screen.seqs
* unique.seqs
* count.seqs
* align.seqs
* filter.seqs
* precluster
* chimera.uchime
* remove.seqs
* classify
* remove.lineage
* cluster.split
* make.shared
* classify.otu






















































