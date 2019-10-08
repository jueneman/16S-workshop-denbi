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
  flash -r 300 ~/workdir/16Sdata/057_R1.fastq ~/workdir/16Sdata/057_R2.fastq -o 057
  
You will get a report on how good that worked out::

  [FLASH] Read combination statistics:
  [FLASH]     Total pairs:      41836
  [FLASH]     Combined pairs:   37934
  [FLASH]     Uncombined pairs: 3902
  [FLASH]     Percent combined: 90.67%

The merged PE reads have now been written to the following file::

  057.extendedFrags.fastq

Let's do that for all other pairs::

  parallel "flash -r 300 ~/workdir/16Sdata/{}_R1.fastq ~/workdir/16Sdata/{}_R2.fastq -o {}" ::: {058,068,074}
  
If you have more information about the amplified fragment, you can adjust min/max overlap as necessary and also provide fragment length and SD if available.


Primer Clipping 
---------------

First we need to know which primer sequences weree used to asmplify our region of interest:

+---------------+--------+--------------------------|
| Domain        | Primer | Sequence                 |
+---------------+------- +--------------------------+
| Bacteria      | F939   | GAATTGACGGGGGCCCGCACAAG  |
| Bacteria      | R1378  | CGGTGTGTACAAGGCCCGGGAACG |
+---------------+--------+--------------------------+

We are going to use cutadapt to search for and remove any found primers sequences from the merged reads:

  mkdir ~/workdir/cutadapt
  cd ~/workdir/cutadapt
  cutadapt -g ^GAATTGACGGGGGCCCGCACAAG ../flash/058.extendedFrags.fastq -e 0.2 -O 10 -o 058.trimmed.fastq


- '^' = anchoring the rpimer at the 5' end of the reads
- '*-e 0.2*' = max error rate of 20%
- '-O *10*' = min overlap of ten bases

```
This is cutadapt 2.4 with Python 3.6.7
Command line parameters: -g ^GAATTGACGGGGGCCCGCACAAG ../flash/058.extendedFrags.fastq -e 0.2 -O 10 -o 058.trimmed.fastq
Processing reads on 1 core in single-end mode ...
[8<----------] 00:00:00        37,934 reads  @     13.5 µs/read;   4.46 M reads/minute
Finished in 0.51 s (14 us/read; 4.43 M reads/minute).

=== Summary ===

Total reads processed:                  37,934
Reads with adapters:                    37,888 (99.9%)
Reads written (passing filters):        37,934 (100.0%)

Total basepairs processed:    18,381,440 bp
Total written (filtered):     17,510,019 bp (95.3%)

=== Adapter 1 ===

Sequence: GAATTGACGGGGGCCCGCACAAG; Type: anchored 5'; Length: 23; Trimmed: 37888 times.

No. of allowed errors:
0-4 bp: 0; 5-9 bp: 1; 10-14 bp: 2; 15-19 bp: 3; 20-23 bp: 4

Overview of removed sequences
length	count	expect	max.err	error counts
19	6	0.0	3	0 0 0 0 6
20	17	0.0	4	0 0 0 16 1
21	28	0.0	4	0 0 22 6
22	321	0.0	4	0 280 39 2
23	37317	0.0	4	36508 785 21 3
24	37	0.0	4	0 29 5 2 1
25	74	0.0	4	0 0 63 3 8
26	88	0.0	4	0 0 0 87 1
```

Quality Treatment – Primer Clipping 
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

Part I: Data Pre-Processing

cd \~/workdir/raw\_data



cat Primers.txt



cutadapt -g \^CTACGGGNGGCWGCAG BGA1\_1.extendedFrags.fastq -o
BGA1\_1.f\_tr.fastq -e 0.2 -O 10 --untrimmed-output
BGA1\_1.f\_utr.fastq





-   cutadapt very useful for primer & adapter trimming
-   Accepts wobble bases
-   Adjust '*stringency*' parameter to your needs
-   Inspect output closely (to many / suspicious trimmed reads)

<!-- -->

-   '*-e 0.2*' = max error rate of 20%
-   '-O *10*' = min overlap of ten bases

--help is your friend

Quality Treatment – Primer Clipping 
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

Part I: Data Pre-Processing

cd \~/workdir/raw\_data



cat Primers.txt



cutadapt -g \^CTACGGGNGGCWGCAG BGA1\_1.extendedFrags.fastq -o
BGA1\_1.f\_tr.fastq -e 0.2 -O 10 --untrimmed-output
BGA1\_1.f\_utr.fastq





Quality Treatment – Primer Clipping 
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

Part I: Data Pre-Processing

cd \~/workdir/raw\_data



cat Primers.txt



cutadapt -g \^CTACGGGNGGCWGCAG BGA1\_1.extendedFrags.fastq -o
BGA1\_1.f\_tr.fastq -e 0.2 -O 10 --trimmed-only



cutadapt -a GGATTAGATACCCBDGTAGTC\$ BGA1\_1.f\_tr.fastq -e 0.2 -O 10
-o BGA1\_1.trimmed.fastq --trimmed-only









Quality Treatment – Quality Trimming 
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^=

Part I: Data Pre-Processing

Trim low quality 3'-ends (and 5'-ends)

-   Based on average q-score within a sliding window

Quality Treatment – Quality Trimming 
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^=

Part I: Data Pre-Processing

sickle se -f BGA1\_1.trimmed.fastq -t sanger -o
BGA1\_1.trimmed.clipped.fastq -q 20 -n







Trim reads
----------

Trim low quality 3'-ends (and 5'-ends)

-   Based on average q-score within a sliding window

<!-- -->

-   '-q 20' = min average quality score of 20
-   '-t sanger' = Phred+33 q-score scale
-   '-n' = truncate at ambiguous (N) base calls

Quality Treatment – Quality Trimming 
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^=

Part I: Data Pre-Processing

sickle se -f BGA1\_1.trimmed.fastq -t sanger -o
BGA1\_1.trimmed.clipped.fastq -q 20 -n







Trim reads
----------

Trim low quality 3'-ends (and 5'-ends)

-   Based on average q-score within a sliding window

<!-- -->

-   '-q 20' = min average quality score of 20
-   '-t sanger' = Phred+33 q-score scale
-   '-n' = truncate at ambiguous (N) base calls

Quality Treatment – Filter Length 
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^=

Part I: Data Pre-Processing

-   Remove reads which are to short (generally)
-   Remove reads out of fragment length (16S hypervariable region)

Quality Treatment – Filter Length 
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^=

Part I: Data Pre-Processing

FastaStats.pl -q BGA1\_1.trimmed.clipped.fastq &gt;
BGA1\_1.trimmed.clipped.fastq.hist



head -n 10 BGA1\_1.trimmed.clipped.fastq.hist

Compute read length histogram
-----------------------------

-   Remove reads which are to short (generally)
-   Remove reads out of fragment length (16S hypervariable region)









Quality Treatment – Filter Length 
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^=

Part I: Data Pre-Processing

FastaStats.pl -q BGA1\_1.trimmed.clipped.fastq &gt;
BGA1\_1.trimmed.clipped.fastq.hist



head -n 10 BGA1\_1.trimmed.clipped.fastq.hist

Compute read length histogram
-----------------------------

-   Remove reads which are to short (generally)
-   Remove reads out of fragment length (16S hypervariable region)









Quality Treatment – Filter Length 
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^=

Part I: Data Pre-Processing

FastaStats.pl -q BGA1\_1.trimmed.clipped.fastq &gt;
BGA1\_1.trimmed.clipped.fastq.hist



head -n 10 BGA1\_1.trimmed.clipped.fastq.hist

Compute read length histogram
-----------------------------

-   Remove reads which are to short (generally)
-   Remove reads out of fragment length (16S hypervariable region)









fastq-mcf -0 -l 367 -L 463 n/a BGA1\_1.trimmed.clipped.fastq -o
BGA1\_1.fastq

Filter on length
----------------

FastQC - Revisited 
^^^^^^^^^^^^^^^^^^^^^=

Part I: Data Pre-Processing

fastqc



Start FastQC
------------

-   … run batch mode on quality treated data
-   … compare the raw with the hq data

Quality Treatment - Pipeline 
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

Part I: Data Pre-Processing

cd \~/workdir/raw\_data



mkdir output



cd output



cp \~/scripts/qc\_pipeline.sh .







-   Exercise:
-   1: put previous commands into one shell script
-   2: execute this script on all PE FASTQ files
-   3: put all in this manner created HQ files in one directory in
-   \~/workdir/HQ

Quality Treatment – Final Remarks 
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^=

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

Part I: Data Pre-Processing

List of Software Used 
^^^^^^^^^^^^^^^^^^^^^^^^=

FastQC

-   *→ http://www.bioinformatics.babraham.ac.uk/projects/fastqc/*

sickle

-   *→ https://github.com/najoshi/sickle*

cutadapt

-   *→ https://code.google.com/p/cutadapt/*

FLASh

-   *→ http://ccb.jhu.edu/software/FLASH/*

ea-utils

-   *→ https://code.google.com/p/ea-utils/*

FASTX-Toolkit

-   *→ http://hannonlab.cshl.edu/fastx\_toolkit/*

Quality Treatment – Primer Clipping 
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

Part I: Data Pre-Processing

cd \~/workdir/raw\_data



cat Primers.txt



cutadapt -g \^CTACGGGNGGCWGCAG BGA1\_1.extendedFrags.fastq -o
BGA1\_1.f\_tr.fastq -e 0.2 -O 10 --untrimmed-output
BGA1\_1.f\_utr.fastq



cutadapt -g \^GACTACHVGGGTATCTAATCC BGA1\_1.f\_utr.fastq -o
BGA1\_1.fr\_tr.fastq -e 0.2 -O 10 --trimmed-only



cutadapt -a GGATTAGATACCCBDGTAGTC\$ BGA1\_1.f\_tr.fastq -e 0.2 -O 10
-o BGA1\_1.trimmed.forward.fastq --trimmed-only



cutadapt -a CTGCWGCCNCCCGTAG\$ BGA1\_1.fr\_tr.fastq -o
BGA1\_1.trimmed.reverse.fastq -e 0.2 -O 10 --trimmed-only



fastx\_reverse\_complement -i BGA1\_1.trimmed.reverse.fastq -o
BGA1\_1.trimmed.flipped.fastq -Q33



cat BGA1\_1.trimmed.forward.fastq BGA1\_1.trimmed.flipped.fastq &gt;
BGA1\_1.trimmed.fastq




