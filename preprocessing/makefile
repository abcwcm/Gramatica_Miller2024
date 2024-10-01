# make -n fastqc
# make -n trim
# make -n sorted_bam
.SECONDEXPANSION:
.SECONDARY:
.DELETE_ON_ERROR:

### OPTIONS
SECOND_PASS = 0

### SYSTEM TOOLS
LN = /bin/ln
MV = /bin/mv
CAT = /bin/cat
MKDIR = /bin/mkdir
ECHO = /bin/echo
CP = /bin/cp
CD = cd

### USER TOOLS
STAR = /athena/abc/scratch/paz2005/bin/src/STAR-2.6.0c/bin/Linux_x86_64_static/STAR #2.6.0c
TRIM_GALORE = /athena/abc/scratch/paz2005/bin/src/TrimGalore-0.5.0/trim_galore # v 0.5.0
SAMTOOLS = /athena/abc/scratch/paz2005/bin/src/samtools-1.8/samtools  #v 1.8
FEATURE_COUNTS = /athena/abc/scratch/paz2005/bin/src/subread-1.6.2-Linux-x86_64/bin/featureCounts #v1.6.2
JAVA = /athena/abc/scratch/paz2005/bin/src/subread-1.6.2-Linux-x86_64/bin/jdk1.8.0_171/bin/java # v 1.8.0_171
FASTQC = /athena/abc/scratch/paz2005/bin/src/FastQC/fastqc #  v0.11.7
QORTS = /athena/abc/scratch/paz2005/bin/src/QoRTs/QoRTs.jar # v1.3.0
RSCRIPT = /home/paz2005/miniconda3/bin/Rscript #v 3.4.3
R = /home/paz2005/miniconda3/bin/R #v 3.4.3

### REFERENCES
REFERENCE = /athena/abc/scratch/paz2005/references/GRCh38.p12
REFERENCE_FA = /athena/abc/scratch/paz2005/references/GRCh38.p12/GRCh38.primary_assembly.genome.fa
ANNOTATION = /athena/abc/scratch/paz2005/references/GRCh38.p12/gencode.v28.annotation.gtf

### PARAMETERS
READ_LENGTH = 
STAR_OPTIONS = --runThreadN 1 \
--runMode alignReads \
--genomeDir $(REFERENCE) \
--readFilesCommand zcat \
--outSAMstrandField intronMotif  \
--outFilterIntronMotifs RemoveNoncanonicalUnannotated  \
--outFilterType BySJout \
--outReadsUnmapped None \
--outSAMtype BAM Unsorted \
--chimOutType SeparateSAMold \
--limitSjdbInsertNsj 10000000 

lc = $(subst A,a,$(subst B,b,$(subst C,c,$(subst D,d,$(subst E,e,$(subst F,f,$(subst G,g,$(subst H,h,$(subst I,i,$(subst J,j,$(subst K,k,$(subst L,l,$(subst M,m,$(subst N,n,$(subst O,o,$(subst P,p,$(subst Q,q,$(subst R,r,$(subst S,s,$(subst T,t,$(subst U,u,$(subst V,v,$(subst W,w,$(subst X,x,$(subst Y,y,$(subst Z,z,$1))))))))))))))))))))))))))


### DO NOT EDIT BELOW THIS LINE UNLESS YOU KNOW WHAT YOU ARE DOING
FASTQFILES :=  $(wildcard *_R1*.fastq.gz)
TRIMMED_FASTQFILES := $(patsubst %.fastq.gz,%_val_1.fastq.gz,$(wildcard *_R1_???.fastq.gz))
SAMPLES := $(sort $(foreach a,$(FASTQFILES),$(firstword $(subst _, ,$a))))
FLOWCELLS := $(sort $(join $(foreach a,$(FASTQFILES),$(firstword $(subst _, ,$a))), $(addprefix _, $(foreach a,$(FASTQFILES),$(word 2, $(subst _, ,$a))))))
BARCODES :=  $(sort $(join $(join $(foreach a,$(FASTQFILES),$(firstword $(subst _, ,$a))), $(addprefix _, $(foreach a,$(FASTQFILES),$(word 2, $(subst _, ,$a))))), $(addprefix _, $(foreach a,$(FASTQFILES),$(word 3, $(subst _, ,$a))))))
LANES := $(sort $(join $(join $(join $(foreach a,$(FASTQFILES),$(firstword $(subst _, ,$a))), $(addprefix _, $(foreach a,$(FASTQFILES),$(word 2, $(subst _, ,$a))))), $(addprefix _, $(foreach a,$(FASTQFILES),$(word 3, $(subst _, ,$a))))), $(addprefix _, $(foreach a,$(FASTQFILES),$(word 4, $(subst _, ,$a))))))
FASTQFILES_MM := $(shell find *_R1*.fastq.gz | grep  mouse)
SAMPLES_MM := $(sort $(foreach a,$(FASTQFILES_MM),$(firstword $(subst _, ,$a))))


all: merged_bam gene_counts qc_plots
fastqc: $(patsubst %.fastq.gz,%_fastqc.zip,$(wildcard *_R1_???.fastq.gz))
trim: $(patsubst %.fastq.gz,%_val_1.fastq.gz,$(wildcard *_R1_???.fastq.gz))
merge_fastq_lanes: $(addsuffix .fastq.gz, $(LANES))
first_pass: $(FASTQFILES:.fastq.gz=.1p.Aligned.out.bam) $(FASTQFILES:.fastq.gz=.1p.SJ.out.tab)
second_pass: $(FASTQFILES:.fastq.gz=.2p.Aligned.out.bam) $(FASTQFILES:.fastq.gz=.2p.SJ.out.tab)
sorted_bam: $(FASTQFILES:.fastq.gz=.sorted.bam)
merged_bam: $(addsuffix .merged.bam, $(SAMPLES))
coordSorted_bam: $(addsuffix .coordSorted.bam, $(SAMPLES))
gene_counts: $(addsuffix .gene.counts, $(SAMPLES)) # gene.counts.txt
qc_data: $(addprefix ./qc/, $(addsuffix /QC.summary.txt, $(SAMPLES)))

test:
	@echo $(SAMPLES_MM)

### MERGE FASTQ FILES BY SAMPLE NAME
find-fastq-files = $(sort $(filter $1_% , $(FASTQFILES)))

define merge-fastq-files
$1.fastq.gz: $(call find-fastq-files,$1)
	$$(if $$(findstring 1, $$(words $$^)),$$(LN) -fs $$^ $$@,$(CAT) $$^ >> $$@)
endef

$(foreach s,$(LANES),$(eval $(call merge-fastq-files,$s)))

	
### FASTQC 
%_fastqc.zip: %.fastq.gz $$(subst R1,R2,%.fastq.gz)
	$(FASTQC) $^

%_fastqc.zip: %.fastq.gz
	$(FASTQC) $<

### TRIM GALORE
%_val_1.fastq.gz: %.fastq.gz $$(subst R1,R2,%.fastq.gz)
	$(TRIM_GALORE) --phred33 --quality 0 --stringency 10 --length 20 --fastqc --paired $^

### FIRST PASS ALIGNMENT
%.1p.Aligned.out.bam %.1p.SJ.out.tab: %.fastq.gz $$(subst R1,R2,%.fastq.gz)
	$(STAR) --genomeLoad LoadAndKeep --readFilesIn $^ --outFileNamePrefix $*.1p. $(STAR_OPTIONS)

%.1p.Aligned.out.bam %.1p.SJ.out.tab: %.fastq.gz
	$(STAR) --genomeLoad LoadAndKeep --readFilesIn $< --outFileNamePrefix $*.1p. $(STAR_OPTIONS)
	
### SORT BAM FILES
ifdef SECOND_PASS
ifeq ($(SECOND_PASS), 1)
%.sorted.bam: %.2p.Aligned.out.bam
	$(SAMTOOLS) sort -n -O bam -T $<.tmp $< > $@
else
%.sorted.bam: %.1p.Aligned.out.bam
	$(SAMTOOLS) sort -n -O bam -T $<.tmp $< > $@
endif
endif

### MERGE BAM FILES BY SAMPLE NAME
find-bam-files = $(sort $(filter $1_% , $(FASTQFILES:.fastq.gz=.sorted.bam)))

define merge-bam-files
$1.merged.bam: $(call find-bam-files,$1)
	$$(if $$(filter 1, $$(words $$^)),$$(LN) -fs $$^ $$@,$(SAMTOOLS) merge -f $$@ $$^)
endef

$(foreach s,$(SAMPLES),$(eval $(call merge-bam-files,$s)))


### COORD SORT
%.coordSorted.bam: %.merged.bam
	$(SAMTOOLS) sort -O bam -T $<.tmp $< > $@ ; $(SAMTOOLS) index $@

### COUNT READS PER GENE
%.gene.counts : %.merged.bam
	file_arr=(./$**R2*.fastq.gz) && if [ -f $${file_arr[0]} ] ; then $(FEATURE_COUNTS) -p -g "gene_name" -a $(ANNOTATION) -o $@  $< ; else $(FEATURE_COUNTS) -g "gene_name" -a $(ANNOTATION) -o $@  $< ; fi