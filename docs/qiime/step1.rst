Import your quality treated data 
--------------------------------

First of all, we will import our already demultiplexed, paired-end assembled and quality filtered reads as a Qiime2 Project. In order to do so, we first need to copy our manifest and mapping files into our workdir::

 cd ~/workdir
 cp ~/workdir/16S-data/HQ/manifest.txt ~/workdir/HQ/
 cp ~/workdir/16S-data/HQ/combined_mapping.txt ~/workdir

Then we use the tools/import function to import our data sets as defined in the manifest::

 qiime tools import \
 --type SampleData[JoinedSequencesWithQuality] \
 --input-format SingleEndFastqManifestPhred33 \
 --input-path HQ/manifest.txt \
 --output-path bga_joined_demux

To get an overview about what got imported and if everything worked as expected, we can visually summarize our poject::

 qiime demux summarize \
 --i-data bga_joined_demux.qza \
 --o-visualization bga_joined_demux.qzv

Then, we ca take the newly generated `bga_joined_demux.qzv` and drag'n'drop it onto the https://view.qiime2.org/ website.

How did QIIME 2 know what happened? 
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

*qza and qzv files are just structured zip files*

-   Data, in the usual formats (e.g., newick, fasta, tsv, ...), is in the data / directory.
-   The rest is human-readable QIIME-specific metadata.

