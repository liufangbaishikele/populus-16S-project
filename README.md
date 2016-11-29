# populus-16S-project

### Download raw-data from SRA database

* Sequence data


```
fastq-dump –split-files SRR3732212 SRR3732224 SRR3732232 SRR3732240 SRR3732248 SRR3732257 SRR3732266 SRR3732268 SRR3732269 SRR3732270 SRR3732271 SRR3732273
```


* Metadata downloaded from [SRA-RunInfo Table link] (https://www.ncbi.nlm.nih.gov/Traces/study/?acc=SRP077616)


### Sequence preprocessing --*cutadapt*

These random sequences ahead forward primers and behind reverse primers are introduced during library preparation, which described in [Practical innovations for high-throughput amplicon sequencing] (http://www.nature.com/nmeth/journal/v10/n10/abs/nmeth.2634.html)


```
Primer sets--16S rRNA V4 region
5’ GTGCCAGCMGCCGCGGTAA
3’ GGACTACHVGGGTWTCTAAT

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

Except for a different treatment.file, the other parameters are set to be the same as MiSeq SOP. During each step, a corresponding directory were built to hold specific output from each step. This could help me to trace back the data and extract useful information for further analysis. Otherwise, we can altenatively use mothur batch file to accelerate the sequece analysis process.


#### Read summary after each step


* make contigs -- get 1018667 contigs
* screen.seqs -- contigs too short or too long are discarded, with 926176 (90.0%) being left.
* unique.seqs -- 135675 unique sequences
* count.seqs -- generated a count table, with unique sequence counts in each sample
* pcr.seqs -- generate a customized reference sequence targeted V4 region, using silva as the reference database.
* align.seqs -- align unique sequences to reference 
* filter.seqs -- to remove overhangs at both ends -- 131327 sequences
* precluster -- futhur de-noise sequence to allow up to 2bp difference between sequences -- 40131 sequences
* chimera.uchime -- find chimeras
* remove.seqs -- remove chimeras--38655 sequences
* classify -- classify sequences to taxonomy group
* remove.lineage -- remove undisired sequences, i.e., chloroplast, mitochondria, Archaea, eukaryote and unknown --38301 left
* cluster.split -- based on classification information to cluster sequences with similarity from 85 to 100% 
* make.shared -- extract OTUs with similarity of 97% -- 13408 OTUs
* classify.otu -- classify the OTUs to taxonomy.

### OTU table

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

### Sample table

```
Sample_ID	Protocol	Compartment
Non_Rhizo_SRX1901989    	Non_enriched	Rhizosphere
Non_Rhizo_SRX1901990   	Non_enriched	Rhizosphere
Non_Rhizo_SRX1901991    	Non_enriched	Rhizosphere
Non_Endo_SRX1901986     	Non_enriched	Endosphere
Non_Endo_SRX1901987   	Non_enriched	Endosphere
Non_Endo_SRX1901988    	Non_enriched	Endosphere
Enrich_Rhizo_SRX1901983	Enriched_method	Rhizosphere
Enrich_Rhizo_SRX1901984	Enriched_method	Rhizosphere
Enrich_Rhizo_SRX1901985 	Enriched_method	Rhizosphere
Enrich_Endo_SRX1901980 	Enriched_method	Endosphere
Enrich_Endo_SRX1901981 	Enriched_method	Endosphere
Enrich_Endo_SRX1901982 	Enriched_method	Endosphere

```

* OTU taxonomy

```
OTU	Kindom	Phylum	Class	Order	Family	Genus
Otu00001	Bacteria	Actinobacteria	Actinobacteria	Actinomycetales	Mycobacteriaceae	Mycobacterium
Otu00002	Bacteria	Firmicutes	Clostridia	Clostridiales	Ruminococcaceae	Clostridium_III
Otu00003	Bacteria	Proteobacteria	Alphaproteobacteria	Rhizobiales	Rhizobiales_unclassified	Rhizobiales_unclassified
Otu00004	Bacteria	Proteobacteria	Alphaproteobacteria	Rhizobiales	Bradyrhizobiaceae	Bradyrhizobium
Otu00005	Bacteria	Proteobacteria	Alphaproteobacteria	Rhizobiales	Phyllobacteriaceae	Phyllobacteriaceae_unclassified
Otu00006	Bacteria	Proteobacteria	Alphaproteobacteria	Rhizobiales	Rhizobiales_unclassified	Rhizobiales_unclassified
Otu00007	Bacteria	Proteobacteria	Alphaproteobacteria	Sphingomonadales	Sphingomonadaceae	Sphingomonas
Otu00008	Bacteria	Proteobacteria	Alphaproteobacteria	Rhizobiales	Rhizobiales_unclassified	Rhizobiales_unclassified
Otu00009	Bacteria	Proteobacteria	Alphaproteobacteria	Alphaproteobacteria_unclassified	Alphaproteobacteria_unclassified	Alphaproteobacteria_unclassified
Otu00010	Bacteria	Verrucomicrobia	Spartobacteria	Spartobacteria_order_incertae_sedis	Spartobacteria_family_incertae_sedis	Spartobacteria_genera_incertae_sedis
Otu00011	Bacteria	Proteobacteria	Alphaproteobacteria	Rhizobiales	Rhizobiaceae	Rhizobium
Otu00012	Bacteria	Proteobacteria	Betaproteobacteria	Burkholderiales	Burkholderiaceae	Burkholderia
Otu00013	Bacteria	Proteobacteria	Betaproteobacteria	Betaproteobacteria_unclassified	Betaproteobacteria_unclassified	Betaproteobacteria_unclassified
Otu00014	Bacteria	Actinobacteria	Actinobacteria	Actinomycetales	Pseudonocardiaceae	Pseudonocardia

```

After OTU calling at 97% similarity, I traced back and summarized sequences counts from each sample 

```
Enrich_Endo_SRX1901980  90522
Enrich_Endo_SRX1901981  36118
Enrich_Endo_SRX1901982  77382
Enrich_Rhizo_SRX1901983 95813
Enrich_Rhizo_SRX1901984 72603
Enrich_Rhizo_SRX1901985 87197
Non_Endo_SRX1901986     31058
Non_Endo_SRX1901987     83905
Non_Endo_SRX1901988     76715
Non_Rhizo_SRX1901989    90546
Non_Rhizo_SRX1901990    81344
Non_Rhizo_SRX1901991    78320

```

*Generate Subsample

Based on the above count summary results, 31058 (lowest counts) are used for sumsample procedur

```
mothur > sub.sample(shared=treatment.0.03.shared,size=31058)

```
* Rarefaction analysis

```
mothur>rarefaction.single(shared=treatment.0.03.shared,calc=sobs-chao-ace,freq=100)
```
output file--treatment.0.03.groups.rarefaction

```
numsampled	0.03-Enrich_Endo_SRX1901980	0.03-Enrich_Endo_SRX1901981	0.03-Enrich_Endo_SRX1901982	0.03-Enrich_Rhizo_SRX1901983	0.03-Enrich_Rhizo_SRX1901984	0.03-Enrich_Rhizo_SRX1901985
1	1	1	1	1	1	1
100	69.728	58.536	67.704	79.688	69.36	64.664
200	123.112	100.408	118.904	141.392	120.928	112.672
300	168.832	136.032	162.856	194.544	165.32	154.016
400	210.672	167.928	201.84	242.568	205.688	190.792
500	248.112	196.44	237.4	287.264	242.232	224
600	282.632	223.696	270.488	327.216	276.088	255.368
700	315.312	248.016	301.504	366.16	308.152	284.176
800	346.12	271.208	329.56	402.76	337.888	311.832
900	374.936	292.712	356.904	435.76	366.976	338.432
1000	402.488	313.168	382.768	468.616	394.416	363.728
1100	429.56	333.4	407.272	499.608	419.856	387.656
1200	454.848	352.336	430.624	530.304	444.616	410.624
1300	479.656	370.056	452.856	559.384	468.432	433.456
1400	503.344	387.408	473.984	587.424	490.96	455.64
1500	526	403.888	494.528	614.224	513.512	476.52
1600	548.2	419.664	514.48	640.064	535.392	496.608

```
 
* Linear Discriminant Effect Size Analysis (LEfSe)-mothur built in software-was used to compare OTU abundance between treatments
 
 Output summary
 > head treatment.subsample.0.03.lefse_summary

```
OTU             LogMaxMean      Class               LDA          p-Value
Otu00001        4.97212        Enrich_Rhizo    4.64003        0.0396024
Otu00002        5.11937        Enrich_Endo     4.83162        0.0153061
Otu00003        4.65756        Enrich_Rhizo    4.23549        0.0216228
Otu00004        4.35499        -
Otu00005        4.39507        Enrich_Rhizo    4.01493        0.0187854
Otu00006        4.25342        Enrich_Endo     3.89033        0.0155644
Otu00007        4.03287         -
Otu00008        4.03027         -
Otu00009        4.31468        Non_Endo        3.95786         0.0498705

```
### Data transfered to local computer using filezilla

### Further data exploration using R packages

* Subsequent analysis 

* Stacked barplot using relative abundance of OTUs

* Sample clustering using Heat map

* NMDS --bacterial community distribution between samples based on Bray-Curtis distance

##  Detailed R cod  ##


source('http://bioconductor.org/biocLite.R')
biocLite('phyloseq')
  library('phyloseq')                    #packageVersion('phyloseq')
  library(ggplot2)                       #packageVersion('ggplot2')
  library(plyr)                          #packageVersion("plyr")
  library(ape)                           #Used for tree file reading and for plot.tree("abc.tree")#packageVersion("ape")
  library(scales)
  
  ### Raw data import
setwd('G:\\UT\\I Love my project\\16S_sra_practice\\EPP622 populus project\\OTU table and taxonomy table generated from mothur')

  ###   OTU table 

    OTU_raw=read.csv('OTU_97_similarity.csv',head=TRUE)
    otu=OTU_raw[,c(5:16)]
    rownames(otu)=OTU_raw[,1]
    
  ###  taxonomy table
    
    Taxonomy_raw<-read.csv('OTU_taxonomy.csv',h=TRUE)
    tax<-Taxonomy_raw[,2:7]
    rownames(tax)=OTU_raw[,1]
    
  ###--- sample table
    
    sample_data_raw<-read.csv("sample_data.csv",h=TRUE)
    sample<-sample_data_raw[,2:3]
    rownames(sample)<-colnames(otu)
  ## check data type to fit for phyloseq class types
    
   class(sample) 
    class(otu)
    class(tax)
    otu<-as.matrix(otu)
    tax<-as.matrix(tax)

### gernerate experimental level class object
    

   library(phyloseq)
    OTU=otu_table(otu,taxa_are_rows=TRUE)
    TAX=tax_table(tax)
    SAMPLE<-sample_data(sample)
    OTU_TAX_SAMPLE<-phyloseq(OTU,SAMPLE,TAX)

### construct integerated dataset

   melt_data<-psmelt(OTU_TAX_SAMPLE)
    colnames(melt_data)
    head(melt_data)
      
### Calculate relative abundance of otu count in each sample

   r_OTU_TAX_SAMPLE<-transform_sample_counts(OTU_TAX_SAMPLE,function(OTU) OTU/sum(OTU))
    head(otu_table(r_OTU_TAX_SAMPLE))
    sum(otu_table(r_OTU_TAX_SAMPLE)[,2]) 
    
    r_melt_data<-psmelt(r_OTU_TAX_SAMPLE)
    colnames(r_melt_data)
    head(r_melt_data)

### Construct dataset for stacked barplot at phylum level using r_melt_data

   phylum_Enrich_Endo_SRX1901980<-plyr::ddply(subset(r_melt_data,Sample=="Enrich_Endo_SRX1901980"),.(Phylum),summarize,otu_richness_1980=length(Phylum),proportion_1980=sum(Abundance))
    phylum_Enrich_Endo_SRX1901981<-plyr::ddply(subset(r_melt_data,Sample=="Enrich_Endo_SRX1901981"),.(Phylum),summarize,otu_richness_1981=length(Phylum),proportion_1981=sum(Abundance))
    phylum_Enrich_Endo_SRX1901982<-plyr::ddply(subset(r_melt_data,Sample=="Enrich_Endo_SRX1901982"),.(Phylum),summarize,otu_richness_1982=length(Phylum),proportion_1982=sum(Abundance))
    
    phylum_Enrich_Rhizo_SRX1901983<-plyr::ddply(subset(r_melt_data,Sample=="Enrich_Rhizo_SRX1901983"),.(Phylum),summarize,otu_richness_1983=length(Phylum),proportion_1983=sum(Abundance))
    phylum_Enrich_Rhizo_SRX1901984<-plyr::ddply(subset(r_melt_data,Sample=="Enrich_Rhizo_SRX1901983"),.(Phylum),summarize,otu_richness_1984=length(Phylum),proportion_1984=sum(Abundance))
    phylum_Enrich_Rhizo_SRX1901985<-plyr::ddply(subset(r_melt_data,Sample=="Enrich_Rhizo_SRX1901983"),.(Phylum),summarize,otu_richness_1985=length(Phylum),proportion_1985=sum(Abundance))
    
    phylum_Non_Endo_SRX1901986<-plyr::ddply(subset(r_melt_data,Sample=="Non_Endo_SRX1901986"),.(Phylum),summarize,otu_richness_1986=length(Phylum),proportion_1986=sum(Abundance))
    phylum_Non_Endo_SRX1901987<-plyr::ddply(subset(r_melt_data,Sample=="Non_Endo_SRX1901987"),.(Phylum),summarize,otu_richness_1987=length(Phylum),proportion_1987=sum(Abundance))
    phylum_Non_Endo_SRX1901988<-plyr::ddply(subset(r_melt_data,Sample=="Non_Endo_SRX1901988"),.(Phylum),summarize,otu_richness_1988=length(Phylum),proportion_1988=sum(Abundance))
    
    phylum_Non_Rhizo_SRX1901989<-plyr::ddply(subset(r_melt_data,Sample=="Non_Rhizo_SRX1901989"),.(Phylum),summarize,otu_richness_1989=length(Phylum),proportion_1989=sum(Abundance))
    phylum_Non_Rhizo_SRX1901990<-plyr::ddply(subset(r_melt_data,Sample=="Non_Rhizo_SRX1901990"),.(Phylum),summarize,otu_richness_1990=length(Phylum),proportion_1990=sum(Abundance))
    phylum_Non_Rhizo_SRX1901991<-plyr::ddply(subset(r_melt_data,Sample=="Non_Rhizo_SRX1901991"),.(Phylum),summarize,otu_richness_1991=length(Phylum),proportion_1991=sum(Abundance))
    
    phylum_proportion_and_richness<-cbind(phylum_Enrich_Endo_SRX1901980,phylum_Enrich_Endo_SRX1901981,phylum_Enrich_Endo_SRX1901982,phylum_Enrich_Rhizo_SRX1901983,phylum_Enrich_Rhizo_SRX1901984,phylum_Enrich_Rhizo_SRX1901985,phylum_Non_Endo_SRX1901986,phylum_Non_Endo_SRX1901987,phylum_Non_Endo_SRX1901988,phylum_Non_Rhizo_SRX1901989,phylum_Non_Rhizo_SRX1901990,phylum_Non_Rhizo_SRX1901991)
    Phylum_richness<-phylum_proportion_and_richness[,c(1,2,5,8,11,14,17,20,23,26,29,32,35)]
    phylum_proportion<-phylum_proportion_and_richness[,c(1,3,6,9,12,15,18,21,24,27,30,33,36)]
    
    library(reshape2)
    library(scales)
    melt_phylum<-melt(phylum_proportion,id.vars="Phylum")
    head(melt_phylum)
    ggplot(melt_phylum,aes(x=variable,y=value,fill=Phylum))+geom_bar(position='stack',stat="identity",width=0.7)+scale_y_continuous(labels=percent_format())+scale_x_discrete(labels=sample_names(OTU_TAX_SAMPLE))+
    theme(axis.text.x=element_text(size = 13,angle=270))+theme(axis.text.y=element_text(size = 13))+xlab("Sample ID")+ylab("Relative abundance")+#Change the ticks labels
    theme(axis.title.x=element_text(size = 13,face="bold"))+theme(axis.title.y=element_text(size = 13,face="bold",angle=90))+# change the axis labels
    ggtitle("Relative abundance of phylum")

### rarefaction curve

 
   library(reshape2)
    rarefaction_intergrated<-melt(rarefaction,id.vars="numsampled")
    head(rarefaction_intergrated)
  ggplot(rarefaction_intergrated,aes(x=numsampled,y=value,colour=variable))+geom_point()+geom_line()+theme(legend.position="top")#+xlim(0,32000)

### Plot heat map using top 500 OTUs
    sub_r_OTU_TAX_SAMPLE=prune_taxa(names(sort(taxa_sums(r_OTU_TAX_SAMPLE),TRUE)[1:500]),r_OTU_TAX_SAMPLE)# 0.003563
    plot_heatmap(sub_r_OTU_TAX_SAMPLE)+scale_x_discrete("Sample", labels = c("Enrich_Endo_SRX1901980" = "Enrich_Endo","Enrich_Endo_SRX1901981" = "Enrich_Endo", "Enrich_Endo_SRX1901982" = "Enrich_Endo","Enrich_Rhizo_SRX1901983" = "Enrich_Rhizo","Enrich_Rhizo_SRX1901984" = "Enrich_Rhizo","Enrich_Rhizo_SRX1901985"="Enrich_Rhizo","Non_Endo_SRX1901986"="Non_Endo","Non_Endo_SRX1901987"="Non_Endo","Non_Endo_SRX1901988"="Non_Endo","Non_Rhizo_SRX1901989"="Non_Rhizo","Non_Rhizo_SRX1901990"="Non_Rhizo","Non_Rhizo_SRX1901991"="Non_Rhizo"))+
    theme(axis.title.x=element_text(size = 13,face="bold"))+theme(axis.title.y=element_text(size = 13,face="bold",angle=90))

## Here is ready for MDS analysis and plot--Based on OTU relative abundance


bray_r<-distance(r_OTU_TAX_SAMPLE,method="bray")
bray_r_PCoA<-ordinate(r_OTU_TAX_SAMPLE,"PCoA",distance="bray")
plot_ordination(r_OTU_TAX_SAMPLE,bray_r_PCoA,color="Protocol",shape="Compartment")+geom_point(size=4)+
  xlab(paste("PCoA1",round(bray_r_PCoA$values$Relative_eig[1]*100,digits=2),"%",sep=" "))+ylab(paste("PCoA2",round(bray_r_PCoA$values$Relative_eig[2]*100,digits=2),"%",sep=""))+
  theme(axis.title.x=element_text(size = rel(1.3)))+theme(axis.title.y=element_text(size = rel(1.3),angle=90))+
  ggtitle("PCoA on Bray-curtis distance using relative abundance") + theme(plot.title = element_text(face="bold",size=rel(1.5))) 

## Sample clustering tree visualization in R
library(ape)
phylogenetic_tree<-read.tree("treatment_0.thetayc.0.03.lt.tre")
phylogenetic_tree
plot(phylogenetic_tree)








