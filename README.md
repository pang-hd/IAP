## IAP [![DOI](https://zenodo.org/badge/19342535.svg)](https://zenodo.org/badge/latestdoi/19342535)
Illumina analysis pipeline.

## Download
Seperate releases can be downloaded here: https://github.com/CuppenResearch/IAP/releases or use git clone:
```bash
git clone git@github.com:CuppenResearch/IAP.git
```

## Usage
IAP is configured using ini files and on run/analysis level using a config file. The idea is to have one ini file per run/analysis type (e.g. exome sequencing). Every setting can be reconfigured in the run/analysis config file. All ini files are located in the [settings subfolder](https://github.com/CuppenResearch/IAP/tree/master/settings). The run/analysis config is created using the illumina_createConfig script and is stored in the ouput directory.
#### View available ini files
```bash
perl illumina_createConfig.pl
```
#### Create config
```bash
perl illumina_createConfig.pl -i <filename.ini> -o </path/to/output_dir> (-f /path/to/fastq_dir OR -b /path/to/bam_dir OR -v /path/to/vcfFile.vcf) -m your@mail.com
```
Input file naming convention:
- Fastq: sample_flowcell_index_lane_R[12]_tag.fastq.gz
- Bam: sample.bam

#### Run pipeline
```bash
perl illumina_pipeline.pl /path/to/output_dir/settings.config>
```

## Dependencies
#### Core tools
- Opengrid engine
- Perl/dev
- Python/dev
- R/dev
- Java 1.7/jre/dev
    - Java 1.8 for gatk >= 3.8

#### Bio tools
- [FastQC](http://www.bioinformatics.babraham.ac.uk/projects/fastqc/)
- [BWA](http://bio-bwa.sourceforge.net/)
- [Sambamba](http://lomereiter.github.io/sambamba/)
- [bamMetrics](https://github.com/CuppenResearch/bamMetrics)
- [GATK (genomeanalysis toolkit) and GATK QUEUE >= 3.2-2](https://www.broadinstitute.org/gatk/)
- [Picard >= 1.119](http://broadinstitute.github.io/picard/)
- [SnpEff / SnpSift](http://snpeff.sourceforge.net/)
- [Samtools](http://www.htslib.org/)
- [Vcftools](http://vcftools.sourceforge.net/)
- [IGVtools](https://www.broadinstitute.org/igv/igvtools)
- [Contra](http://contra-cnv.sourceforge.net/)
- [FREEC](http://bioinfo-out.curie.fr/projects/freec/)
- [QDNAseq](https://github.com/ccagc/QDNAseq) (used tag: v1.9.2-HMF.1)
- [ExomeDepth](https://github.com/UMCUGenetics/ExomeDepth)
- [Varscan](http://varscan.sourceforge.net/)
- [Strelka](https://sites.google.com/site/strelkasomaticvariantcaller/)
- [Freebayes](https://github.com/ekg/freebayes)
- [Tabix](http://www.htslib.org/doc/tabix.html)
- [vcflib](https://github.com/ekg/vcflib)
- [delly](https://github.com/tobiasrausch/delly/)
- [manta](https://github.com/Illumina/manta)
- [plink](http://pngu.mgh.harvard.edu/~purcell/plink/)
- [king](http://people.virginia.edu/~wc9c/KING/)
- [bcftools](https://samtools.github.io/bcftools/)
- [VT](http://genome.sph.umich.edu/wiki/Vt)
- [hmftools](https://github.com/hartwigmedical/hmftools)

#### Perl modules
- strict
- POSIX
- Getopt::Long
- FindBin
- File::Path
- Number::Format

#### R packages
- ggplot2
- knitr
- markdown
- reshape
- xtable
- tools
- brew

#### Using R > 3.0
Add the folowing to your .Rprofile (~/.Rprofile) when using R > 3.0. This will solve a known gatk bug with queue job reports (http://gatkforums.broadinstitute.org/discussion/4405/problem-with-queue-job-reports-in-3-2-0).
```bash
.First<-function(){
library(grid)
}
```

## Ini settings
Overview and explanation of all ini settings. See [dummy.ini](https://github.com/CuppenResearch/IAP/blob/master/settings/illumina_pipeline.ini.dummy) for a complete example.

```
#### CLUSTER CONFIGURATION ####
CLUSTER_PATH	/srv/sge/fedor8/common
CLUSTER_TMP	/tmp | path to cluster tmp folder
CLUSTER_RESERVATION	yes/no | use -R option when submitting jobs
CLUSTER_PROJECT	project_name | project name used for submitting jobs
QUEUE_RETRY	yes/no | Retry failed queue jobs once

#### DEFAULT TOOL PATHS ####
BWA_PATH	/path/to/bwa
SAMBAMBA_PATH	/path/to/sambamba
QUEUE_PATH	/path/to/queue | gatk > 3.2-2
PICARD_PATH	/path/to/picard
BAMMETRICS_PATH	/path/to/bammetrics
FASTQC_PATH	/path/to/fastqc
GATK_PATH	/path/to/gatk | gatk > 3.2-2
SNPEFF_PATH /path/to/snpeff
VCFTOOLS_PATH	/path/to/vcftools
IGVTOOLS_PATH	/path/to/igvtools

GATK_JAVA_MODULE	Temporay fix to allow for different java versions using modules. Probably will be replaced by GNU GUIX.

#### MODES ####
PRESTATS	yes/no
MAPPING	yes/no
POSTSTATS	yes/no
INDELREALIGNMENT	yes/no
BASEQUALITYRECAL	yes/no
VARIANT_CALLING	yes/no
SOMATIC_VARIANTS	yes/no
SV_CALLING	yes/no
COPY_NUMBER	yes/no
BAF	yes/no
FINGERPRINT	yes/no
CALLABLE_LOCI	yes/no
FILTER_VARIANTS	yes/no
ANNOTATE_VARIANTS	yes/no
VCF_UTILS	yes/no
NIPT	yes/no
CHECKING	yes/no

#### GENOME SETTINGS ####
GENOME	/path/to/genome.fasta

####SOMATIC SAMPLE REGEX####
## Only required for somatic variant calling, copy number and structural variant analysis
SOMATIC_REGEX	(CPCT\d{8})([TR][IVX]*$)
SOMATIC_REGEX_REF_CODE	R #comma separated list of ref origin values
SOMATIC_REGEX_TUMOR_CODE	T #comma separated list of tumor origin values
### SOMATIC_REGEX should follow this patern: (<sample_match>)(<origin_match>)
# R = reference
# T = tumor

#### PRESTATS CLUSTER CONFIGURATION ####
PRESTATS_QUEUE	queue_name
PRESTATS_TIME	estimated runtime
PRESTATS_THREADS	number_of_threads
PRESTATS_MEM	maximum_memory

#### MAPPING CLUSTER CONFIGURATION ####
MAPPING_QUEUE	queue_name
MAPPING_TIME	estimated runtime
MAPPING_THREADS	number_of_threads
MAPPING_MEM	maximum_memory
MAPPING_SETTINGS	-c 100 -M

MARKDUP_LEVEL	lane/sample/no | Mark duplicates per lane, per sample (merged lanes) or not at all.
MARKDUP_QUEUE	queue_name
MARKDUP_TIME	estimated runtime
MARKDUP_THREADS	number_of_threads
MARKDUP_MEM	maximum_memory
MARKDUP_OVERFLOW_LIST_SIZE	Size of the overflow list, for more information see sambamba docs.

#### FLAGSTAT CONFIGURATION ####
# Used for mapping, realignment and recalibration.
FLAGSTAT_QUEUE	queue_name
FLAGSTAT_THREADS	number_of_threads
FLAGSTAT_TIME		estimated runtime
FLAGSTAT_MEM		maximum_memory

#### POSTSTATS CLUSTER CONFIGURATION ####
POSTSTATS_QUEUE	queue_name
POSTSTATS_TIME	estimated runtime
POSTSTATS_THREADS	number_of_threads
POSTSTATS_MEM	maximum_memory
POSTSTATS_COVERAGECAP	250 | Coverage cap only used when no target file is supplied (wgs)
POSTSTATS_TARGETS	/path/to/targets.bed | Targets bed file must be compatible with picard
POSTSTATS_BAITS	/path/to/baits.bed | Baits bed file must be compatible with picard

EXONCALLCOV	yes/no
EXONCALLCOV_QUEUE	queue_name
EXONCALLCOV_TIME	estimated runtime
EXONCALLCOV_MEM	off or maximum_memory
EXONCALLCOV_PATH	/path/to/exoncov.py
EXONCALLCOV_BED	/path/to/bed
EXONCALLCOV_PREF	/path/to/Preferred_transcript_list.txt
EXONCALLCOV_PANEL	path/to/gpanels.txt
EXONCALLCOV_ENS	/path/to/NM_ENSEMBL_HGNC.txt

#### REALIGNMENT CLUSTER CONFIGURATION ####
REALIGNMENT_MASTER_QUEUE	queue_name
REALIGNMENT_MASTER_TIME	estimated runtime
REALIGNMENT_MASTER_THREADS	number_of_threads
REALIGNMENT_MASTER_MEM	maximum_memory
REALIGNMENT_QUEUE	queue_name
REALIGNMENT_THREADS	number_of_threads
REALIGNMENT_TIME	estimated runtime
REALIGNMENT_MERGETHREADS	number_of_threads
REALIGNMENT_MEM	maximum_memory
REALIGNMENT_SCALA	QScripts/IndelRealigner.scala
REALIGNMENT_SCATTER	number_of_scatters
REALIGNMENT_MODE	single/multi | multi or single sample realignment mode
REALIGNMENT_KNOWN	GATK_bundle/1000G_phase1.indels.b37.vcf	GATK_bundle/Mills_and_1000G_gold_standard.indels.b37.vcf | common indel files supplied by gatk

####RECALIBRATION CLUSTER CONFIGURATION####
BASERECALIBRATION_MASTER_QUEUE	queue_name
BASERECALIBRATION_MASTER_TIME	estimated runtime
BASERECALIBRATION_MASTER_THREADS	number_of_threads
BASERECALIBRATION_MASTER_MEM	maximum_memory
BASERECALIBRATION_QUEUE	queue_name
BASERECALIBRATION_TIME	estimated runtime
BASERECALIBRATION_THREADS	number_of_threads
BASERECALIBRATION_MEM	maximum_memory
BASERECALIBRATION_SCALA	QScripts/BaseRecalibrator.scala
BASERECALIBRATION_SCATTER	number_of_scatters
BASERECALIBRATION_KNOWN	GATK_bundle/1000G_phase1.indels.b37.vcf	GATK_bundle/dbsnp_137.b37.vcf	GATK_bundle/Mills_and_1000G_gold_standard.indels.b37.vcf | common indel and snp files supplied by gatk
BASERECALIBRATION_QC	no/yes | generate QC data to analyse covariation remaining after recalibration

####CALLING CLUSTER CONFIGURATION####
CALLING_MASTERQUEUE	queue_name
CALLING_MASTERTHREADS	number_of_threads
CALLING_QUEUE	queue_name
CALLING_TIME	estimated runtime
CALLING_THREADS	number_of_threads
CALLING_MEM	maximum_memory
CALLING_SCATTER	number_of_scatters
CALLING_SCALA	QScripts/HaplotypeCaller.scala
CALLING_GVCF	no/yes
CALLING_GVCFGQBANDS	5,10,15,20,30,40,50,60 | Required if CALLING_GVCF == yes
CALLING_SEXAWARE	no/yes | Enable sex aware calling, only in combination with gvcf mode and human data
CALLING_DBSNP	GATK_bundle/dbsnp_137.b37.vcf | common snp file supplied by gatk
CALLING_STANDCALLCONF	10 | The minimum phred-scaled confidence threshold at which variants should be called. Gatk default = 10
CALLING_PLOIDY	2 | Ploidy (number of chromosomes) per sample. For pooled data, set to (Number of samples in each pool * Sample Ploidy).
CALLING_TARGETS	/path/to/target.interval_list | Optional, use for targeted data e.g. exome.
CALLING_INTERVALPADDING	20 | Optional, only use in combination with calling_targets
CALLING_UGMODE	BOTH | Optional, only used when calling with unified genotyper.

####VARIANT FILTER CLUSTER CONFIGURATION####
FILTER_MASTERQUEUE	queue_name
FILTER_MASTERTHREADS	number_of_threads
FILTER_QUEUE	queue_name
FILTER_TIME	estimated runtime
FILTER_THREADS	number_of_threads
FILTER_MEM	maximum_memory
FILTER_SCATTER	10
FILTER_SCALA	QScripts/HardFilter.scala
FILTER_MODE	BOTH/SNP/INDEL | Filter all variants, only snps or only indels.
FILTER_SNPTYPES	SNP/NO_VARIATION/MNP
FILTER_INDELTYPES	INDEL/MIXED
FILTER_SNPNAME	LowQualityDepth	MappingQuality	StrandBias	HaplotypeScoreHigh	MQRankSumLow	ReadPosRankSumLow | SNP filter names
FILTER_SNPEXPR	QD < 2.0	MQ < 40.0	FS > 60.0	HaplotypeScore > 13.0	MQRankSum < -12.5	ReadPosRankSum < -8.0 | SNP filters
FILTER_INDELNAME	LowQualityDepth	StrandBias	ReadPosRankSumLow | Indel filter names
FILTER_INDELEXPR	QD < 2.0	FS > 200.0	ReadPosRankSum < -20.0 | Indel filters
FILTER_CLUSTERSIZE	3 | Optional, The number of SNPs which make up a cluster
FILTER_CLUSTERWINDOWSIZE	35 | Optional, The window size (in bases) in which to evaluate clustered SNPs

####SOMATIC VARIANT CONFIGURATION####
SOMVAR_TARGETS	/path/to/target.bed | Optional, use for targeted data e.g. exome.

## Strelka
SOMVAR_STRELKA	yes/no
STRELKA_PATH	/path/to/strelka
STRELKA_INI	/path/to/strelka/strelka_config_bwa_exome.ini
STRELKA_QUEUE	queue_name
STRELKA_TIME	estimated runtime
STRELKA_THREADS	number_of_threads
STRELKA_MEM	maximum_memory

## Varscan
SOMVAR_VARSCAN	yes/no
VARSCAN_PATH	/path/to/varscan.jar
TABIX_PATH /path/to/tabix/
VARSCAN_QUEUE	queue_name
VARSCAN_TIME	estimated runtime
VARSCAN_THREADS	number_of_threads
VARSCAN_MEM	maximum_memory
VARSCAN_SETTINGS	--min-var-freq 0.1 --strand-filter 1 | Varscan settings
VARSCAN_POSTSETTINGS	--p-value 0.05 | Varscan post settings

## Freebayes
SOMVAR_FREEBAYES	yes/no
FREEBAYES_PATH	/path/to/freebayes/bin
BIOVCF_PATH	/path/to/biovcf/
VCFLIB_PATH /path/to/vcflib/
VT_PATH	/path/to/vt
FREEBAYES_QUEUE	queue_name
FREEBAYES_TIME	estimated runtime
FREEBAYES_THREADS	number_of_threads
FREEBAYES_MEM	maximum_memory
FREEBAYES_SETTINGS	-C 3 --pooled-discrete --genotype-qualities --min-coverage 5 --no-mnps --no-complex | Freebayes settings
FREEBAYES_SOMATICFILTER	--filter 'r.tumor.dp>=20 and r.normal.dp>=20 and r.info.ssc>=20 and qual>=10' --sfilter 's.gq>=15' | biovcf somatic filter settings
FREEBAYES_GERMLINEFILTER	--filter 'r.tumor.dp>=20 and r.normal.dp>=20 and qual>=10' --sfilter 's.gq>=15' | biovcf germline filter settings

## Mutect
SOMVAR_MUTECT	yes/no
MUTECT_PATH	/path/to/mutect/
MUTECT_MEM	maximum_memory
MUTECT_QUEUE	queue_name
MUTECT_TIME	estimated runtime
MUTECT_THREADS	number_of_threads
MUTECT_COSMIC	/path/to/CosmicCodingMuts_v72.vcf.gz
#MUTECT_SCALA	IAP/QScripts/Mutect.scala
#MUTECT_SCATTER	number_of_scatters
#MUTECT_MASTERQUEUE	queue_name
#MUTECT_MASTERTHREADS	number_of_threads

## Merge vcfs
SOMVARMERGE_QUEUE	queue_name
SOMVARMERGE_TIME	estimated runtime
SOMVARMERGE_THREADS	number_of_threads
SOMVARMERGE_MEM	maximum_memory

#### SV Calling CONFIGURATION####

##MANTA
SV_MANTA	yes/no
MANTA_PATH	/path/to/manta/bin
MANTA_QUEUE	queue_name
MANTA_TIME	estimated runtime
MANTA_THREADS	number_of_threads
MANTA_MEM	maximum_memory

##DELLY
SV_DELLY	no/yes
DELLY_PATH	/path/to/delly_v0.6.7
DELLY_QUEUE	queue_name
DELLY_TIME	estimated runtime
DELLY_THREADS	number_of_threads
DELLY_MEM	maximum_memory
DELLY_MERGE_QUEUE	queue_name
DELLY_MERGE_TIME	estimated runtime
DELLY_MERGE_MEM	maximum_memory
DELLY_MERGE_THREADS	number_of_threads

DELLY_SVTYPE	DEL	DUP	INV	TRA
DELLY_SPLIT	no/yes	no/yes	no/yes	yes/no
DELLY_MAPQUAL	1
DELLY_MAD	9
DELLY_FLANK	13
#DELLY_VCF_GENO
DELLY_GENO_QUAL	5

####COPY NUMBER VARIANTION CONFIGURATION####
CNVCHECK_QUEUE	queue_name
CNVCHECK_TIME	estimated runtime
CNVCHECK_THREADS	number_of_threads
CNVCHECK_MEM	maximum_memory
CNV_MODE	sample_control
CNV_TARGETS	/path/to/target.bed | Optional, use for targeted data e.g. exome.

## QDNASEQ
CNV_QDNASEQ	yes
QDNASEQ_QUEUE	queue_name
QDNASEQ_TIME	estimated runtime
QDNASEQ_THREADS	number of threads
QDNASEQ_MEM	maximum memory
QDNASEQ_PATH	/hpc/local/CentOS7/cog_bioinf/QDNAseq_v1.9.2-HMF.1

## EXOMEDEPTH
CNV_EXOMEDEPTH	yes/no
EXOMEDEPTH_QUEUE	queue_name
EXOMEDEPTH_TIME	estimated runtime
EXOMEDEPTH_THREADS	number of threads
EXOMEDEPTH_MEM	maximum memory
EXOMEDEPTH_PATH	/hpc/diaggen/software/development/Dx_resources_ED/ExomeDepth/run_ExomeDepth.py

## Contra
CNV_CONTRA	yes/no
CONTRA_THREADS	number_of_threads
CONTRA_QUEUE	queue_name
CONTRA_TIME	estimated runtime
CONTRA_MEM	maximum_memory
CONTRA_PATH	/hpc/local/CentOS6/cog_bioinf/CONTRA.v2.0.6/
CONTRA_FLAGS	--nomultimapped --largeDeletion --plot

CONTRA_VISUALIZATION	yes
CONTRA_PLOTSCRIPT	/hpc/cog_bioinf/data/annelies/CNVanalysis/CNA.pl
CONTRA_PLOTDESIGN	wes

## FREEC
CNV_FREEC	yes/no
FREEC_THREADS	number_of_threads
FREEC_QUEUE	queue_name
FREEC_TIME	estimated runtime
FREEC_MEM	maximum_memory
FREEC_PATH	/path/to/freec
FREEC_CHRLENFILE	/path/to/genome.len
FREEC_CHRFILES	/path/to/chr_files
FREEC_PLOIDY	2 | Ploidy (number of chromosomes) per sample.
FREEC_WINDOW	1000 | explicit window size (higher priority than coefficientOfVariation )
FREEC_TELOCENTROMERIC	50000 | length of pre-telomeric and pre-centromeric regions: Control-FREEC will not output small CNAs and LOH found within these regions (they are likely to be false because of mappability/genome assembly issues)
50000 for human/mouse genomes.
FREEC_MAPPABILITY_TRACK	Optional mappability track

#### B ALLELE FREQUENCY CLUSTER CONFIGURATION####
BAF_QUEUE	queue_name
BAF_TIME	estimated runtime
BAF_THREADS	number_of_threads
BAF_MEM	maximum_memory
BIOVCF_PATH	/path/to/biovcf/bin
BAF_SNPS	/path/to/CytoScanHD/CytoScanHD_hg19_SNPs_sorted.bed

####FINGERPRINT CONFIGURATION####
FINGERPRINT_QUEUE	queue_name
FINGERPRINT_THREADS	number of threads
FINGERPRINT_MEM	maximum_memory
FINGERPRINT_TIME	estimate runtime
FINGERPRINT_TARGET	/path/to/fingerprint_design.vcf

#### CALLABLE LOCI CLUSTER CONFIGURATION####
CALLABLE_LOCI_QUEUE	queue_name
CALLABLE_LOCI_TIME	estimated runtime
CALLABLE_LOCI_THREADS	number_of_threads
CALLABLE_LOCI_MEM	maximum_memory
## CALLABLE LOCI filter settings based on haplotype caller settings
CALLABLE_LOCI_BASEQUALITY	10
CALLABLE_LOCI_MAPQUALITY	10
CALLABLE_LOCI_DEPTH	20
CALLABLE_LOCI_DEPTHLOWMAPQ	20

####VARIANT ANNOTATION CONFIGURATION####
ANNOTATE_QUEUE	queue_name
ANNOTATE_TIME	estimated runtime
ANNOTATE_THREADS	number_of_threads
ANNOTATE_MEM	maximum_memory
## SnpEff
ANNOTATE_SNPEFF	yes/no
ANNOTATE_DB	GRCh37.74 | Snpeff annotation database
ANNOTATE_FLAGS	-hgvs -lof -no-downstream -no-upstream -no-intergenic | Snpeff flags
## SnpSift
ANNOTATE_SNPSIFT	yes/no
ANNOTATE_DBNSFP	/path/to/dbNSFP.txt.gz
ANNOTATE_FIELDS	| List of fields to annotate, see dummy.ini for an example.
## Annotate Frequencies eg. GONL
ANNOTATE_FREQUENCIES	yes/no
ANNOTATE_FREQNAME	GoNLv5 | Info field name
ANNOTATE_FREQDB	/path/to/vcf.gz
ANNOTATE_FREQINFO	AF,AN,AC | Fields to annotate vcf with.
## GATK Annotate ID's
ANNOTATE_IDFIELD	yes/no
ANNOTATE_IDNAME	ID name for header
ANNOTATE_IDDB	/path/to/id.vcf

####VCF UTILS CONFIUGARTION#####
VCFUTILS_QUEUE	queue_name
VCFUTILS_TIME	estimated runtime
VCFUTILS_THREADS	number_of_threads
VCFUTILS_MEM	maximum_memory
VCFUTILS_KINSHIP	yes/no
PLINK_PATH	/path/to/plink
KING_PATH	/path/to/king
VCFUTILS_PHASE	yes/no
VCFUTILS_GENDERCHECK	yes/no
PED	/path/to/ped_file_folder/
VCFUTILS_ROH	yes/no
BCFTOOLS_PATH	/path/to/bcftools-1.4
ROH_SETTINGS	--AF-dflt 0.4 --ignore-homref --skip-indels
VCFUTILS_SINGLE_SAMPLE_VCF	yes/no

####NIPT CLUSTER CONFIGURATION####
CHROMATE_PATH	/path/to/chromate.py
NIPT_REFERENCESET	/path/to/reference_set/
NIPT_QUEUE	queue_name
NIPT_TIME	estimated runtime
NIPT_MEM	mmaximum_memory
NIPT_THREADS	number_of_threads
NIPT_MASTER_QUEUE	queue_name
NIPT_MASTER_TIME	estimated runtime
NIPT_MASTER_MEM	maximum_memory
NIPT_MASTER_THREADS	number_of_threads

####CHECKING CLUSTER CONFIGURATION####
CHECKING_QUEUE	queue_name
CHECKING_TIME	estimated runtime
CHECKING_THREADS	number_of_threads
CHECKING_RM	list,of,files,to,remove
CHECKING_CLEANUP	yes/no
CHECKING_CLEANUP_SCRIPT	/path/to/cleanup_script.py

```
