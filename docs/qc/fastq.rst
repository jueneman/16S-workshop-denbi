FASTQ Format 
------------
Let's first have a look on what a FASTQ file looks like and who its format is defined:

.. image:: https://github.com/jueneman/16S-workshop-denbi/blob/master/docs/qc/pics/fastq.png

Sequence Quality Scores 
^^^^^^^^^^^^^^^^^^^^^^^

Sequence quality scores originated form the phred base caller used for Sanger sequencing:

.. image:: https://github.com/jueneman/16S-workshop-denbi/blob/master/docs/qc/pics/chromatogram.png

+-----------------------------------+-----------------------------------+
|     Quality value                 |     Error probability             |
+-----------------------------------+-----------------------------------+
|     20                            |     1/100                         |
|     30                            |     1/1000                        |
|     40                            |     1/10000                       |
|     50                            |     1/100000                      |
+-----------------------------------+-----------------------------------+

- Quality = log-transformed error probability

  
References
^^^^^^^^^^

**Images** https://openlab.citytech.cuny.edu/bio-oer/analyzing-dna/next-gen-sequencing/2/
