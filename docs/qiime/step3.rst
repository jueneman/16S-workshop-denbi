Openref OTU Clustering
----------------------

Before we can start and do referenced based clustering, the actual reference OTUs must be provided from a database.
For this tutorial, we have take the reference sequences along with their taxonomic annotation from:

- https://www.arb-silva.de/download/archive/qiime/

and

- http://qiime.org/home_static/dataFiles.html
- https://docs.qiime2.org/2019.7/data-resources/

All requeired files have been uploaded to the public object storage in the cloud. To download it, do::

 cd ~/workdir
 wget https://openstack.cebitec.uni-bielefeld.de:8080/swift/v1/mgcourse_data/16S-database.tgz
 tar -xvzf 16S-database.tgz
 rm tar -xvzf 16S-database.tgz
 cp ~/DATA/database/silva_128/97/97_otus_16S.fasta .

Now we import these reference data into our qiime project::

  cd ~/workdir/qiime
  qiime tools import --type FeatureData[Sequence] \
  --input-path ~/workdir/16Sdatabase/silva_132_97_16S.fna \
  --output-path silva_132_97_16S.qza

  cd ~/workdir/qiime
  qiime tools import --type FeatureData[Taxonomy] \
  --input-format HeaderlessTSVTaxonomyFormat \
  --input-path ~/workdir/16Sdatabase/consensus_taxonomy_7_levels.txt \
  --output-path consensus_taxonomy_all_levels.qza

Now that everything is prepared, we can initiate the clustering process as implemented in vsearch::

  qiime vsearch cluster-features-open-reference \
  --i-table bga_joined_demux_derep_nonch_table.qza \
  --i-sequences bga_joined_demux_derep_nonch.qza \
  --o-clustered-table bga_oref_table.qza \
  --o-clustered-sequences bga_oref_seqs.qza \
  --o-new-reference-sequences bga_oref_newref_seqs.qza \
  --p-perc-identity 0.97 \
  --p-threads 14 \
  --i-reference-sequences silva_132_97_16S.qza \
  --verbose

This will take some time (even hours for very large data sets)! Then we can look at the results::

  qiime feature-table summarize \
  --i-table bga_oref_table.qza \
  --o-visualization bga_oref_table.qz
