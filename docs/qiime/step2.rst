Chimera Detection with VSearch
------------------------------

Vsearch is an alternative to the USEARCH tool developed by Robert C. Edgar (2010). It offers:

- have open source code with an appropriate open source license
- be free of charge, gratis
- have a 64-bit design that handles very large databases and much more than 4GB of memory
- be as accurate or more accurate than usearch
- be as fast or faster than usearch

De-replicate Reads
^^^^^^^^^^^^^^^^^^

De-replication is the process of collapsing identical reads into single read representation while preserving the number of identical reads in some kind of mapping files. There are dozens of tools which can perform de-replication, also all sequence based clustering tools using an identity cutoff of 100%, but we will use vsearch for that::

   cd ~/workdir/qiime
   qiime vsearch dereplicate-sequences \ 
   --i-sequences bga_joined_demux.qza \
   --o-dereplicated-table bga_joined_demux_derep_table.qza \
   --o-dereplicated-sequences bga_joined_demux_derep.qza \
   --verbose
 
 
In order to inspect to what extent the de-replication process reduced the datasets, we can again visualize the results.
First on the feature table::
  
   cd ~/workdir/qiime
   qiime feature-table summarize \
   --i-table bga_joined_demux_derep_table.qza \
   --o-visualization bga_joined_demux_derep_table.qza
 
Then on the reads::
 
   cd ~/workdir/qiime
   qiime feature-table tabulate-seqs \
   --i-data bga_joined_demux_derep.qza \
   --o-visualization bga_joined_demux_derep.qza

This time, let's use the q2studio to inspect the results. To start q2studio, open a new terminal and::

   cd ~/workdir/qiime
   conda activate mgcourse
   q2studio
 
Then scroll down and find the visualizations, which can be selected to show the results in a new window.

Filter Chimeras I
^^^^^^^^^^^^^^^^^

Vsearch function for chimera filtering is also integrated into qiime and can be called directly as a qiime function::

   cd ~/workdir/qiime
   qiime vsearch uchime-denovo \
   --i-sequences bga_joined_demux_derep.qza \
   --i-table bga_joined_demux_derep_table.qza \   
   --verbose 

This will create three files::

   cd ~/workdir/qiime
   ls -larth .
   ...
   -rw-rw-r--  1 ubuntu ubuntu  429631 Oct  8 20:13 chimeras.qza
   -rw-rw-r--  1 ubuntu ubuntu 3060915 Oct  8 20:13 nonchimeras.qza
   -rw-rw-r--  1 ubuntu ubuntu 1543519 Oct  8 20:13 stats.qza

The chimeras and nonchimeras are again FeatureData[Sequences], so raw reads. To get a detailed look at the filtering process, we need to summarize the stats.qza file, which is a Metadata file::

   cd ~/workdir/qiime
   qiime metadata tabulate \
   --m-input-file stats.qza \
   --o-visualization stats.qzv

You can inspect the results using q2studio.

Finally, we exlude all chimeras from our de-replicated data set, both the reads and the feature table (the mapping file on the sequence frequencies)::

   cd ~/workdir/qiime
   qiime feature-table filter-features \
   --i-table  bga_joined_demux_derep_table.qza \
   --m-metadata-file nonchimeras.qza \
   --o-filtered-table bga_joined_demux_derep_nonch_table.qza

   qiime feature-table filter-seqs \
   --i-data bga_joined_demux_derep.qza \
   --m-metadata-file nonchimeras.qza \
   --o-filtered-data bga_joined_demux_derep_nonch.qza

And visualize what we have done::

   qiime feature-table summarize \
   --i-table bga_joined_demux_derep_nonch_table.qza \
   --m-sample-metadata-file combined_mapping.txt \
   --o-visualization bga_joined_demux_derep_nonch_table.qzv
