Quality Treatment 
-----------------

- Reads contain errors (0.1-15%) and contamination
- Quality matters!?

  - NGS high throughput = lots of data
  - The more data the more errors (systematic errors)
  - 16S data &gt; WGS read-based &gt; WGS assembly-based
  - Better data = lower computational cost
  - Decrease of false positives
  - But: always trade-off (false negatives)   
   
- Quality based filtering vs error correction
- Many many tools available


Typical workflow:
^^^^^^^^^^^^^^^^^

.. image:: https://github.com/jueneman/16S-workshop-denbi/blob/master/docs/qc/pics/workflow.png

For this exercise:

- Only 16S data
- Merge: FLASh
- Clip primers: cutadapt
- Trim quality: sickle
- Filter length: ea-utils

Merge reads
-----------

- Assembly of forward and reverse read pairs (if original DNA fragment short than 2x read length)   
- Ungapped alignment with *min overlap* region (favors Illumina)
- Quality scores at merged positions recalculated (abs difference)


Let's try to merge the first pair of reads::

  mkdir -p ~/workdir/flash
  cd ~/workdir/flash
  flash -r 300 ~/workdir/16Sdata/BGA1_1_R1.fastq ~/workdir/16Sdata/BGA1_1_R2.fastq -o BGA1_1
  
You will get a report on how good that worked out::

  [FLASH] Read combination statistics:
  [FLASH]     Total pairs:      8495
  [FLASH]     Combined pairs:   8388
  [FLASH]     Uncombined pairs: 107
  [FLASH]     Percent combined: 98.74%

The merged PE reads have now been written to the following file::

  BGA1_1.extendedFrags.fastq

Let's do that for all other pairs::

  parallel "flash -r 300 ~/workdir/16Sdata/{}_R1.fastq ~/workdir/16Sdata/{}_R2.fastq -o {}" ::: {BGA1_2,BGA2_1,BGA2_2,BGA3_1,BGA3_2,BGA4_1,BGA4_2}
  
If you have more information about the amplified fragment, you can adjust min/max overlap as necessary and also provide fragment length and SD if available.


Primer Clipping 
---------------

First we need to know which primer sequences weree used to asmplify our region of interest

+---------------+--------+--------------------------+
| Domain        | Region | Sequence                 |
+===============+========+==========================+
| Bacteria      | V3F    | CTACGGGNGGCWGCAG         |
| Bacteria      | V4R    | GACTACHVGGGTATCTAATCC    |
+---------------+--------+--------------------------+

We are going to use cutadapt to search for and remove any found primers sequences from the merged reads::

   mkdir ~/workdir/cutadapt
   cd ~/workdir/cutadapt
   cutadapt -g ^CTACGGGNGGCWGCAG ../flash/BGA1_1.extendedFrags.fastq -e 0.2 -O 10 -o BGA1_1.trimmedf.fastq


- '^' = anchoring the rpimer at the 5' end of the reads
- '*-e 0.2*' = max error rate of 20%
- '-O *10*' = min overlap of ten bases

-  cutadapt very useful for primer & adapter trimming
-  Accepts wobble bases
-  Adjust '*stringency*' parameter to your needs
-  Inspect output closely (to many / suspicious trimmed reads)

Let's have a look at our first attempt::

  === Summary ===

  Total reads processed:                   8,388
  Reads with adapters:                     8,383 (99.9%)
  Reads written (passing filters):         8,388 (100.0%)

  Total basepairs processed:     3,808,321 bp
  Total written (filtered):      3,665,848 bp (96.3%)

  === Adapter 1 ===

  Sequence: CTACGGGNGGCWGCAG; Type: anchored 5'; Length: 16; Trimmed: 8383 times.

  No. of allowed errors:
  0-4 bp: 0; 5-9 bp: 1; 10-14 bp: 2; 15 bp: 3

  Overview of removed sequences
  length	count	expect	max.err	error counts
  15	4	0.0	3	0 1 0 3
  16	65	0.0	3	22 6 37
  17	8282	0.0	3	0 8114 168
  18	29	0.0	3	0 0 27 2
  19	3	0.0	3	0 0 0 3



Now we trim off the reverse primer::

   mkdir ~/workdir/cutadapt
   cd ~/workdir/cutadapt
   cutadapt -a GACTACHVGGGTATCTAATCC$ BGA1_1.trimmedf.fastq -e 0.2 -O 10 -o BGA1_1.trimmedfr.fastq

Now, apparently that didn't worked out. The problem is, that the primer is given 5'-3' and by merging our reads the reverse reads now is the reverse complement of the original read, so the primer als needs to be reverse complemented.

Let us quickly do that by creating a new fasta file and call `rev`::

  cd ~/workdir
  echo -e ">primer\nGACTACHVGGGTATCTAATCC" > revprimer.fas
  revseq -sequence revprimer.fas -outseq revprimer_rc.fas
  cat revprimer_rc.fas
  
We can use the correct primer now to trim our reads at the 3' end::

   cd ~/workdir/cutadapt
   cutadapt -a GGATTAGATACCCBDGTAGTC$ BGA1_1.trimmedf.fastq -e 0.2 -O 10 -o BGA1_1.trimmedfr.fastq

Finally, we do that for all of our datasets::

  cd ~/workdir/cutadapt
  parallel "cutadapt -g ^CTACGGGNGGCWGCAG ../flash/{}.extendedFrags.fastq -e 0.2 -O 10 -o {}.trimmedf.fastq" ::: {BGA1_2,BGA2_1,BGA2_2,BGA3_1,BGA3_2,BGA4_1,BGA4_2}
  parallel "cutadapt -a GGATTAGATACCCBDGTAGTC$ {}.trimmedf.fastq -e 0.2 -O 10 -o {}.trimmedfr.fastq" ::: {BGA1_2,BGA2_1,BGA2_2,BGA3_1,BGA3_2,BGA4_1,BGA4_2}
  

Quality Trimming 
----------------

Usually, reads with very low quality consist of many miscalled bases, which can influence any consecutive processing step by inflating cluster numbers or decreasing alignment quality. Therefore, we are going to trim of low quality 3'-ends (and 5'-ends).

For that we use sickle, which trims based on average q-score within a sliding window approach::

  mkdir -p ~/workdir/sickle
  cd ~/workdir/sickle
  sickle se -f ../cutadapt/BGA1_1.trimmedfr.fastq -t sanger -q20 -o BGA1_1.clipped.fastq

-  '-q 20' = min average quality score of 20
-  '-t sanger' = Phred+33 q-score scale
-  '-n' = truncate at ambiguous (N) base calls

Again, we do that for all our data sets::

  cd ~/workdir/sickle
  parallel "sickle se -f ../cutadapt/{}.trimmedfr.fastq -t sanger -q20 -o {}.clipped.fastq" ::: {BGA1_2,BGA2_1,BGA2_2,BGA3_1,BGA3_2,BGA4_1,BGA4_2}

Lenght Filtering
----------------

Finally, we will filter out all reads which are to short (generally) or which out of the fragment length (16S hypervariable region). In order to determine the low and high boundry of this filtering step, we will use a small Perl script which generates a read length histrogram and calculates some basic statistics.

Pls download that script first::

  cd $CONDA_PREFIX/bin
  wget https://openstack.cebitec.uni-bielefeld.de:8080/swift/v1/mgcourse_data/FastaStats.pl
  chmod u+x FastaStats.pl

Now we call it on our FastQ file::

  mkdir ~/workdir/length
  cd  ~/workdir/length  
  FastaStats.pl -q ../sickle/BGA1_1.clipped.fastq > BGA1_1.fastq.hist
  head -n 10 057.fastq.hist

We trim now our reads based on a 1.5*IQR window::

  fastq-mcf -0 -l 369 -L 461 n/a ../sickle/BGA1_1.clipped.fastq -o BGA1_1.fastq
  
- low = 369
- high = 461
- n/a = we don't wanna use primer clipping, therefore don't provide such a file

And again we run this on all of our data::

  parallel "FastaStats.pl -q ../sickle/{}.clipped.fastq > {}.fastq.hist" ::: {BGA1_2,BGA2_1,BGA2_2,BGA3_1,BGA3_2,BGA4_1,BGA4_2}
  grep IQR *.hist
  fastq-mcf -0 -l 369 -L 461 n/a ../sickle/BGA1_2.clipped.fastq -o BGA1_2.fastq
  parallel "fastq-mcf -0 -l 372 -L 460 n/a ../sickle/{}.clipped.fastq -o {}.fastq" ::: {BGA2_1,BGA2_2}
  parallel "fastq-mcf -0 -l 364 -L 464 n/a ../sickle/{}.clipped.fastq -o {}.fastq" ::: {BGA3_1,BGA3_2}
  parallel "fastq-mcf -0 -l 377 -L 449 n/a ../sickle/{}.clipped.fastq -o {}.fastq" ::: {BGA4_1,BGA4_2}


FastQC - Revisited 
------------------

We can now inspect how this basic quality treatment affected the raw read qiality by comparing the results to our previous FastQC reports::

  cd ~/workdir/fastq
  fastqc -t 14 -o ~/workdir/fastqc/ ~/workdir/length/*.fastq
  
Final Remarks 
^^^^^^^^^^^^^

Know your data

-   Library preparation
-   Fragment/read length, possible primers/adapters

Consider sequencing platform (e.g. Illumina vs. Ion Torrent)

Carefully inspect (intermediate) results

Try different strategies (conservative vs. loose parameters)

Adapt to field of research (16S vs read-based vs assembly-based)

Presented workflow only one approach

-   Use other tools if you like
-   Try different workflow order (e.g. merge last)

But always examine raw sequencing data!



References
^^^^^^^^^^

- FastQC:  *→ http://www.bioinformatics.babraham.ac.uk/projects/fastqc/*
- Sickle:  *→ https://github.com/najoshi/sickle*
- cutadapt: *→ https://code.google.com/p/cutadapt/*
- FLASh: *→ http://ccb.jhu.edu/software/FLASH/*
- ea-utils: *→ https://code.google.com/p/ea-utils/*



