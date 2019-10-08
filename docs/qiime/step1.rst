
Import your quality treated data 
--------------------------------

First of all, we will import our already demultiplexed, paired-end assembled and quality filtered reads as a Qiime2 Project. In order to do so, we first need to copy our manifest and mapping files into our workdir::


**&gt; cd ~/workdir**

**&gt; cp ~/16Sdata/HQ/manifest.txt HQ/**

**&gt; cp ~/16Sdata/combined_mapping.txt .**

&gt;qiime demux summarize 

--i-data bga_joined_demux.qza 

--o-visualization bga_joined_demux.qzv

&gt;cp bga_joined_demux.qzv ~/www

Inspect results:

Split your libraries 
^^^^^^^^^^^^^^^^^^^^

**&gt; cd ~/workdir**

**&gt; cp -r ~/DATA/HQ/ .**

**&gt; cp ~/DATA/combined_mapping.txt .**

Import your quality treated data 
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

**&gt; qiime tools import **

**--type SampleData[JoinedSequencesWithQuality] **

**--source-format SingleEndFastqManifestPhred33 **

**--input-path HQ/manifest.txt **

**--output-path bga_joined_demux**

&gt;qiime demux summarize 

--i-data bga_joined_demux.qza 

--o-visualization bga_joined_demux.qzv

&gt;cp bga_joined_demux.qzv ~/www

Inspect results:

**&gt; cd ~/workdir**

**&gt; cp -r ~/DATA/HQ/ .**

**&gt; cp ~/DATA/combined_mapping.txt .**

How did QIIME 2 know what happened? 
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

*qza and qzv files are just structured zip files*

-   Data, in the usual formats (e.g., newick, fasta, tsv, ...), is in
    the data/ directory.
-   The rest is human-readable QIIME-specific metadata.

Errors in PCR 
^^^^^^^^^^^^=

Distinction between artefacts (chimeras, contamination, primer dimer, …)
and bias (reagents, barcode, primers, ...)

Correlation with

-   PCR cycles
-   Annealing temperature
-   Elongation time

PCR conditions are crucial

Hinders post-processing, reduces comparability

No standardized error handling pipeline

Chimeric Formations 
^^^^^^^^^^^^^^^^^^=

**Two possaible reasons:**

-   **By incomplete extension**
-   **By template switching**

**Positive correlation with PCR cycles (saturation)**

**Negative correlation with sample diversity**

**Typically chimeras amounts to a lesser fraction**

Chimeric Formations 
^^^^^^^^^^^^^^^^^^=

**Incomplete Extension Type A**

**Incomplete Extension Type B**

Chimeric Formations 
^^^^^^^^^^^^^^^^^^=

**Template Switch Type A**

**Template Switch Type B**

Chimera Detection 
^^^^^^^^^^^^^^^^^

**Before NGS**

-   **manually curated + semi automated methods**
-   **Data base screening**
-   **Requiring almost full length + slow**

**After NGS**

**High throughput + short reads**

**de novo (intrinsic) and reference based (extrinsic)**

**Most widely used tool: usearch (formerly uchime)**

-   → utilized in qiime (and mothur, and ...)

Chimera Detection with UCHIME 
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

Edgar et al. 2011, Bioinformatics

Bottom up approach

-   Sequences are split
-   chunks are separately compared against non-chimeric sequences
-   Best hits = candidate parents
-   Two best candidates by all chunks are identified
-   Chimeric if matches against different parent
-   Three-way multiple alignment
-   Chimera reported if combined segment of AB has higher identity to
    query than either A or B

Any ref DB suitable which is chimera free (e.g. Greengenes, Silva gold)

De novo mode starts with empty reference database

Filter Potential Chimeric Sequences I 
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^=

**&gt;qiime vsearch dereplicate-sequences **

**--i-sequences bga_joined_demux.qza **

**--o-dereplicated-table bga_joined_demux_derep_table.qza
--o-dereplicated-sequences bga_joined_demux_derep.qza**

****

****

**&gt;qiime vsearch uchime-denovo **

**--i-sequences bga_joined_demux_derep.qza **

**--i-table bga_joined_demux_derep_table.qza **

**--output-dir vsearch-cc-out **

**--verbose**

&gt;qiime metadata tabulate 

-m-input-file vsearch-cc-out/stats.qza 

--o-visualization vsearch-cc-out/stats.qzv

&gt;cp vsearch-cc-out/stats.qzv ~/www

Inspect results:

Filter Potential Chimeric Sequences II 
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

**&gt;qiime feature-table filter-features **

**--i-table bga_joined_demux_derep_table.qza **

**--m-metadata-file vsearch-cc-out/nonchimeras.qza **

**--o-filtered-table bga_joined_demux_derep_nonch_table.qza**

****

**&gt;qiime feature-table filter-seqs **

**--i-data bga_joined_demux_derep.qza **

**--m-metadata-file vsearch-cc-out/nonchimeras.qza **

**--o-filtered-data bga_joined_demux_derep_nonch.qza**

&gt;qiime feature-table summarize 

--i-table bga_joined_demux_derep_nonch_table.qza 

--m-sample-metadata-file combined_mapping.txt 

--o-visualization bga_joined_demux_derep_nonch_table.qzv

&gt;cp bga_joined_demux_derep_nonch_table.qzv ~/www

Inspect results:

 

**OTU Table**

****

****

(i.e. per sample OTU counts)

**Phylogenetic Tree**

Evolutionary relationship between OTUs

Pre-Processing

e.g. remove primer(s), demultiplex, q-filter, CC

Denoise 454 Data

PyroNoise, Denoiser

**Sequencing output**

(Illumina, 454, Sanger)

FASTA, QUAL, sff/trace files

**Metadata**

Mapping file

SRA Submission

Submit sequences and metadata to SRA

Pick OTUs and representative sequences

**Reference based**

usearch, uclust, MOTHUR, blast

**De novo**

****

Usearch, uclust, CD-HIT, MOTHUR

**Open reference**

****

Combination of reference based and De novo approach

Build OTU table

i.e., per sample OTU counts

Build phylogenetic tree

e.g., FastTree, RAxML, ClearCut

Align sequences

e.g., PyNAST, infernal, MUSCLE, MAFFT

Assign taxonomy

Blast, RDP Classifier, uclust consensus-based

α-diversity and rarefraction

e.g., Simpson, chao1, observed species, Shannon

β-diversity and rarefraction

e.g., weighted und unweighted UniFrac, Bray-Curtis, Jaccard

Visualization

e.g., 2D and 3D PCoA plots, distance histograms, taxonomy pie charts/bar
charts/are charts, rarefaction plots, OTU network visualization,
jackknified hierarchical clustering

Navas-Molina et al. 2013, Meth. Enzymol.

OTUs – Operational Taxonomic Unit 
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

Operational Taxonomic Unit (OTU): "the thing(s) being studied“ In
traditional numerical taxonomy (Sokal and Sneath, 1963; Sneath and
Sokal, 1973)

A “Thing“:

-   one individual organism
-   named taxonomic group (species or genus)
-   group with undetermined evolutionary relationships sharing a given
    set of observed characteristics

OTUs – Operational Taxonomic Unit 
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

Operational Taxonomic Unit (OTU): "the thing(s) being studied“ In
traditional numerical taxonomy (Sokal and Sneath, 1963; Sneath and
Sokal, 1973)

A “Thing“:

-   one individual organism
-   named taxonomic group (species or genus)
-   group with undetermined evolutionary relationships sharing a given
    set of observed characteristics

OTUs – Operational Taxonomic Unit 
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

**Operational taxonomic units are more generally referred to as
features.**

OTUs – Operational Taxonomic Unit 
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

OTUs – Operational Taxonomic Unit 
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

Clustering

-   Remove noisy sequences and reduce the amount of sequences to process
-   Works based on a given threshold, i.e. 97% similarity but other
    exist like Oligotyping
-   There are different methods (closed or open reference) and
    algorithms (sortmerna, vclust)

Remove noise

-   Find the cleanest sequence
-   Correct and/or discard super noisy sequences
-   Examples are: DADA2 and Deblur

OTUs – Operational Taxonomic Unit 
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

Open reference based OTU clustering Prepare Reference OTUs 
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^=

For referenced based clustering, reference OTUs must be provided.

→ taken from: <https://www.arb-silva.de/download/archive/qiime/>

→ look for more: http://qiime.org/home_static/dataFiles.html

**&gt; cd ~/workdir**

**&gt; cp ~/DATA/database/silva_128/97/97_otus_16S.fasta .**

**&gt; cp
~/DATA/database/silva_128/97/consensus_taxonomy_all_levels.txt .**

****

****

**&gt; qiime tools import --type FeatureData[Sequence] **

**--input-path 97_otus_16S.fasta **

**--output-path 97_otus_16S.qza**

****

****

**&gt; qiime tools import --type FeatureData[Taxonomy] **

**--source-format HeaderlessTSVTaxonomyFormat **

**--input-path consensus_taxonomy_all_levels.txt **

**--output-path consensus_taxonomy_all_levels.qza**

****

****

****

Open reference based OTU clustering 
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

Cluster and post-process your quality controlled and merged input
sequences using **vsearch**

**qiime vsearch cluster-features-open-reference **

**--i-table bga_joined_demux_derep_nonch_table.qza **

**--i-sequences bga_joined_demux_derep_nonch.qza **

**--o-clustered-table bga_oref_table.qza **

**--o-clustered-sequences bga_oref_seqs.qza **

**--o-new-reference-sequences bga_oref_newref_seqs.qza **

**--output-dir openref **

**--p-perc-identity 0.97 **

**--p-threads 14 **

**--i-reference-sequences 97_otus_16S.qza **

**--verbose**

****

****

****

This will take some time (even hours for very large data sets)!

Compute your OTUs! 
^^^^^^^^^^^^^^^^^^

Final step: Cluster your sequences into OTUs (operational taxonomical
units) for final analysis, such as:

-   Taxonomic Profiling
-   Heat maps based on metadata
-   Alpha diversity
-   Beta diversity
-   Rarefaction analysis
-   ...

OTU clustering 
^^^^^^^^^^^^^^

OTU are clusters of sequences identified by sequence similarity:

resulting resolution defines taxonomic level

-   99% strain (uncommon, uncertain)
-   97% species (desired)
-   95% genus

OTU picking strategies within qiime 
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

*de novo*

-   Applicable, if no reference set is given
-   All reads are preserved
-   Reads are clustered within themselves
-   Can be slow & prone to chimeras
-   Problematic for non-overlapping VRs & huge data

 <http://qiime.org/tutorials/otu_picking.html>

Navas-Molina et al. 2013, Meth. Enzymol.

OTU picking strategies within qiime 
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

closed reference

-   Reference set is needed
-   Only known sequences considered
-   Fast
-   Reference (pre-filtered, chim free, assigned taxa):
    Greengenes(13_8)

Navas-Molina et al. 2013, Meth. Enzymol.

 <http://qiime.org/tutorials/otu_picking.html>

OTU picking strategies within qiime 
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

open reference

Combination of both

Good for high amount of data (can be slow)

Pre-filter

-   Low identity threshold (&gt;60%) against reference dataset → failing
    reads are discarded

Four step clustering

-   Closed ref OTU picking → failing reads used in step2
-   Subsample failed reads, de novo clustering, pick representatives →
    new refset2
-   Closed ref OTU picking against refset2 → failing reads used in step4
-   De novo clustering of failed reads

 <http://qiime.org/tutorials/otu_picking.html>

Navas-Molina et al. 2013, Meth. Enzymol.

OTU picking strategies within qiime 
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

open reference

Post-OTU processing

-   Merge OTU maps from previous steps into one table
-   Filter singletons → only OTUs with size &gt;2 are considered
    (threshold adjustable)
-   Pick one representatives per OTU & assign taxonomy
-   Align representatives with PyNast → construct a phylogenetic tree
-   Filter OTUs from OTU table that failed alignment

 <http://qiime.org/tutorials/otu_picking.html>

Navas-Molina et al. 2013, Meth. Enzymol.

Greedy (online) clustering with UCLUST 
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

Agglomerative hierarchical clustering

-   Complete linkage prone to over compact clusters
-   Single linkage prone to chaining
-   Average linkage inexact cluster distance

Seldom used for OTU clustering (requires NxN similarity matrix, thus
computational expensive)

http://www.drive5.com/usearch/manual/linkage.html

Greedy (online) clustering with UCLUST 
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

Sequences are processed on by one

-   First seq = first centroid
-   If next seq matches any centroid
-   → assigned to this cluster
-   Otherwise forms new centroid

http://www.drive5.com/usearch/manual/uclust_algo.htm

-   Order of processing important
-   Seqs are presorted based on de-replication counts or pre-clustering
    (99%)
-   Only works for sufficiently shared similarity

 

**OTU Table**

****

****

(i.e. per sample OTU counts)

**Phylogenetic Tree**

Evolutionary relationship between OTUs

Pre-Processing

e.g. remove primer(s), demultiplex, q-filter, CC

Denoise 454 Data

PyroNoise, Denoiser

**Sequencing output**

(Illumina, 454, Sanger)

FASTA, QUAL, sff/trace files

**Metadata**

Mapping file

SRA Submission

Submit sequences and metadata to SRA

Pick OTUs and representative sequences

**Reference based**

usearch, uclust, MOTHUR, blast

**De novo**

****

Usearch, uclust, CD-HIT, MOTHUR

**Open reference**

****

Combination of reference based and De novo approach

Build OTU table

i.e., per sample OTU counts

Build phylogenetic tree

e.g., FastTree, RAxML, ClearCut

Align sequences

e.g., PyNAST, infernal, MUSCLE, MAFFT

Assign taxonomy

Blast, RDP Classifier, uclust consensus-based

α-diversity and rarefraction

e.g., Simpson, chao1, observed species, Shannon

β-diversity and rarefraction

e.g., weighted und unweighted UniFrac, Bray-Curtis, Jaccard

Visualization

e.g., 2D and 3D PCoA plots, distance histograms, taxonomy pie charts/bar
charts/are charts, rarefaction plots, OTU network visualization,
jackknified hierarchical clustering

Navas-Molina et al. 2013, Meth. Enzymol.

Amplicon Sequence Variants (ASV) – zero OTUs (zOTUs) – Sequence Variants (SVs) 
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

https://www.nature.com/articles/nmeth.3869

Amplicon Sequence Variants (ASV) – zero OTUs (zOTUs) – Sequence Variants (SVs) 
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

https://www.nature.com/articles/nmeth.3869

https://www.nature.com/articles/ismej2017119

Amplicon Sequence Variants (ASV) – zero OTUs (zOTUs) – Sequence Variants (SVs) 
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

https://www.nature.com/articles/nmeth.3869

https://www.nature.com/articles/ismej2017119

https://www.biorxiv.org/content/early/2016/10/15/081257

DADA2 
^^^^^

R package to infers exact amplicon sequence variants (ASVs)

replacing the coarser OTU clustering approach

input demultiplexed FASTQ

output SVs + sample-wise abundances

-   Filter and trim data
-   Error correction after deducing parametric error model
-   Dereplication
-   Merge pairs
-   Chimera filtering

Taxonomic classification via RDP naive Bayesian classifier

-   genus-species assignment by exact matching

→ https://benjjneb.github.io/dada2/tutorial.html

Generate ASVs with DaDa2 
^^^^^^^^^^^^^^^^^^^^^^^^

**qiime tools import **

**--type SampleData[PairedEndSequencesWithQuality] **

**--source-format PairedEndFastqManifestPhred33 **

**--input-path raw_data/manifest.txt **

**--output-path bga_demux.qza**

****

****

**qiime dada2 denoise-paired **

**--i-demultiplexed-seqs bga_demux.qza **

**--o-table bga_dada2_table.qza **

**--o-representative-sequences bag_dada2_seqs.qza **

**--p-trim-left-f 16 **

**--p-trim-left-r 21 **

**--p-trunc-len-f 250 **

**--p-trunc-len-r 250 **

**--p-n-threads 14 **

**--p-n-reads-learn 200000 **

**--verbose**

****

****

****

****

Taxonomic Classification 
^^^^^^^^^^^^^^^^^^^^^^^^

****

**wget
https://data.qiime2.org/2018.2/common/gg-13-8-99-515-806-nb-classifier.qza**

****

**qiime feature-classifier classify-sklearn **

**--i-classifier gg-13-8-99-515-806-nb-classifier.qza **

**--i-reads bga_oref_seqs.qza **

**--o-classification bga_oref_taxonomy.qza**

****

**qiime feature-classifier classify-sklearn **

**--i-classifier gg-13-8-99-515-806-nb-classifier.qza **

**--i-reads bga_dada2_seqs.qza **

**--o-classification bga_dada2_taxonomy.qza**

****

****

****

****

****

Resources 
^^^^^^^^^

[**http://qiime.org/tutorials/tutorial.html**](http://qiime.org/tutorials/tutorial.html)
----------------------------------------------------------------------------------------

****
----

****
----

**… now better use:**
---------------------

****
----

[**https://docs.qiime2.org/**](https://docs.qiime2.org/)
--------------------------------------------------------

****
----

****
----
