# populus-16S-project

### Download raw-data from SRA database

* Sequence data


```
fastq-dump –split-files SRR3732212 SRR3732224 SRR3732232 SRR3732240 SRR3732248 SRR3732257 SRR3732266 SRR3732268 SRR3732269 SRR3732270 SRR3732271 SRR3732273
```


* Metadata downloaded from [SRA-RunInfo Table link] (https://www.ncbi.nlm.nih.gov/Traces/study/WebEnv=NCID_1_2922414_130.14.18.97_5555_1478249302_1408550304_0MetA0_S_HStore&query_key=4)


### Sequence preprocessing --*cutadapt*

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


### Sequence processing--[MiSeq-SOP] (www.mothur.org/wiki/MiSeq_SOP)

#### Treatment table 

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

#### Commands 

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

### OTU table--clusterred using similarity at 97%

* OTU table
```
label   Group   numOtus Otu00001        Otu00002        Otu00003        Otu00004        Otu00005        Otu00006   ...
0.03    Enrich_Endo_SRX1901980  13408   7164    6737    2930    951     735     1919    10      1605    257     137  ...
0.03    Enrich_Endo_SRX1901981  13408   5141    5841    573     1403    332     595     115     295     15      266  ...
0.03    Enrich_Endo_SRX1901982  13408   4290    12065   1207    1396    613     1223    435     501     306     111  ...
0.03    Enrich_Rhizo_SRX1901983 13408   4872    4427    3878    405     1486    1150    185     1214    708     727  ...
0.03    Enrich_Rhizo_SRX1901984 13408   7593    5061    2919    1731    1512    995     1750    622     164     395  ...
0.03    Enrich_Rhizo_SRX1901985 13408   10804   4919    4856    2239    3337    1216    246     871     143     935  ...
0.03    Non_Endo_SRX1901986     13408   90      5       377     335     100     157     7       299     558     155  ...
0.03    Non_Endo_SRX1901987     13408   373     0       1243    3076    478     577     2148    724     2210    607  ...
0.03    Non_Endo_SRX1901988     13408   414     0       876     1559    345     323     292     364     1262    515  ...
0.03    Non_Rhizo_SRX1901989    13408   140     256     563     310     218     144     110     411     447     234  ...
0.03    Non_Rhizo_SRX1901990    13408   572     195     1142    882     242     238     2246    315     530     205  ...
0.03    Non_Rhizo_SRX1901991    13408   330     283     847     573     246     172     344     254     545     307  ...
```

* OTU taxonomy

```
OTU     Size    Taxonomy
Otu00001        41783   Bacteria(100);"Actinobacteria"(100);Actinobacteria(100);Actinomycetales(100);Mycobacteriace$
Otu00002        39789   Bacteria(100);Firmicutes(100);Clostridia(100);Clostridiales(100);Ruminococcaceae(100);Clost$
Otu00003        21411   Bacteria(100);"Proteobacteria"(100);Alphaproteobacteria(100);Rhizobiales(100);Rhizobiales_u$
Otu00004        14860   Bacteria(100);"Proteobacteria"(100);Alphaproteobacteria(100);Rhizobiales(100);Bradyrhizobia$
Otu00005        9644    Bacteria(100);"Proteobacteria"(100);Alphaproteobacteria(100);Rhizobiales(100);Phyllobacteri$
Otu00006        8709    Bacteria(100);"Proteobacteria"(100);Alphaproteobacteria(100);Rhizobiales(100);Rhizobiales_u$
Otu00007        7888    Bacteria(100);"Proteobacteria"(100);Alphaproteobacteria(100);Sphingomonadales(100);Sphingom$
Otu00008        7475    Bacteria(100);"Proteobacteria"(100);Alphaproteobacteria(100);Rhizobiales(100);Rhizobiales_u$
Otu00009        7145    Bacteria(100);"Proteobacteria"(100);Alphaproteobacteria(100);Alphaproteobacteria_unclassifi$
Otu00010        6831    Bacteria(100);"Verrucomicrobia"(100);Spartobacteria(100);Spartobacteria_order_incertae_sedi$
Otu00011        5813    Bacteria(100);"Proteobacteria"(100);Alphaproteobacteria(100);Rhizobiales(100);Rhizobiaceae($
Otu00012        5436    Bacteria(100);"Proteobacteria"(100);Betaproteobacteria(100);Burkholderiales(100);Burkholder$
Otu00013        5368    Bacteria(100);"Proteobacteria"(100);Betaproteobacteria(100);Betaproteobacteria_unclassified$
Otu00014        5179    Bacteria(100);"Actinobacteria"(100);Actinobacteria(100);Actinomycetales(100);Pseudonocardia$
Otu00015        5170    Bacteria(100);"Proteobacteria"(100);Deltaproteobacteria(100);Deltaproteobacteria_unclassifi$
Otu00016        5151    Bacteria(100);"Acidobacteria"(100);Acidobacteria_Gp6(100);Acidobacteria_Gp6_order_incertae_$
Otu00017        5008    Bacteria(100);"Proteobacteria"(100);"Proteobacteria"_unclassified(100);"Proteobacteria"_unc$
Otu00018        4958    Bacteria(100);"Proteobacteria"(100);Deltaproteobacteria(100);Desulfuromonadales(100);Geobac$

```
### Export to local computer

*Subsequent analysis* 

* Barplot using relative abundance of OTU

* Sample clustering using Heat map

* NMDS and PCoA--bacterial community distribution between samples

* LEfSe: Linear Discriminant Effect Size Analysis --look at the most difference taxon between treatment

* Generate a Venn diagrams to see how much percent of the OTUs are shared between treatment















































