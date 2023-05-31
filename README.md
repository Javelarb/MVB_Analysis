This repository is for the Dry Beans Culturing project which assess the microbial community composition of cultures grown in the presence and absence of dry beans.\
\* Two types of beans where analyzed, pinto vs black.\
\* These beans were ground into a fine powder and then added to BHI media at 4 g/L.\
\* There was limited solubility to the BHI + bean media and over time the components separated into a soluble and insoluble fraction.\
\* The effect of the soluble vs. insoluble bean fraction was also investigated.\

Methods:
Cultures were allowed to grow for 24 hours anaerobically at 37C following inoculation. For the inoculum, a pea sized portion of fecal material was dissolved into 5 mL of reduced PBS and 40 uL was used to inoculate 2 mL of reduced BHI in a 96 well plate format. Plates were sealed and allowed to incubate. OD600 was taken before and after incubation, and the difference was used to calculate microbial growth. Controls included plain media, and media + fiber with no inoculum. After, DNA was extracted from samples using the ZymoBIOMICS DNA Miniprep Kit (Cat# D4300) according to the manufacturers suggest protocol. The DNA was prepared for sequencing using an adapted protocol based on the Illumina DNA Prep kit (Cat# 20018705). Library preparation controls included water and a synthetic microbial DNA standard from ZymoResearch (Cat# D6306). The final library was quantified using the Quant-iT™ PicoGreen™ dsDNA Assay Kit (Cat# P7589) and D1000 ScreenTape Assay for Agilent TapeStation Systems (Cat# 5067-1504) according to the manufacturer’s protocols. The final library concentration was 7.85 ng/uL with an average fragment size of 531 bases. Sequencing was performed using an Illumina Novoseq 6000 with an S4, 300 cycle reagent kit. The resultant reads were paired-end, 150 nucleotides in length. Raw sequencing data was quality filtered using the bbduk.sh script from the BBMap suite of tools. Then, reads were dereplicated using the dedupe.sh script, also from the BBMap suite. Lastly, human and mouse-derived reads were removed from samples with BowTie2. Taxonomic assignment of sequences was performed using Kraken2 and Bracken. Figures were produced using ggplot2 in R.\

Low volume protocol:\
https://www.protocols.io/view/low-volume-methodology-for-nextera-dna-flex-librar-dm6gpr2r8vzp/v1.\

The following parameters were used for quality filtering in bbduk.sh\

bbduk.sh \
in=${dir}/raw_data/symlinks/${prefix}.1.fastq.gz \
in2=${dir}/raw_data/symlinks/${prefix}.2.fastq.gz \
ref=adapters,artifacts,phix,lambda,pjet,mtst,kapa \
out=${dir}/output/bbduk/${prefix}_QF.1.fastq.gz \
out2=${dir}/output/bbduk/${prefix}_QF.2.fastq.gz \
stats=${dir}/output/bbduk/${prefix}_stats.txt \
refstats=${dir}/output/bbduk/${prefix}_refstats.txt \
minavgquality=20 \
hammingdistance=1 \
tpe \
tbo \
forcetrimleft=20 \
threads=8

No additional parameters beyond the defaults were used with dedupe.sh\

dedupe.sh \
in=${dir}/output/bbduk/${prefix}_QF.1.fastq.gz \
in2=${dir}/output/bbduk/${prefix}_QF.2.fastq.gz \
out=${dir}/output/dedupe/${prefix}_DD.fastq.gz \
threads=8

To remove host genomes, bowtie2 was used to align reads to the human + mouse + rat genomes and the reads that did not align were kept\

bowtie2 -p 16 \
-x ${dir}/output/mouse_rat_human_combined_genomes/bt2_combined_genome_index \
-1 ${dir}/output/dedupe/${prefix}_DD.1.fastq.gz \
-2 ${dir}/output/dedupe/${prefix}_DD.2.fastq.gz \
--un-conc-gz ${dir}/output/decon/${prefix}

For Whiteson lab members, the fastq sequences and scripts can be found on the HPC at:\
/dfs7/whitesonlab/Julio_Bean_Culturing_05152023/

Or\

The lab google drive folder.\