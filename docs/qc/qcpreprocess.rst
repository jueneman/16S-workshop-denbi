Quality Control & Pre-processing
--------------------------------

FASTQ Format 
^^^^^^^^^^^^^^^^

Let's first have a look on what a FASTQ file looks like and who its format is defined:

![](https://github.com/jueneman/16S-workshop-denbi/blob/master/docs/qc/pics/fastq.png)

Sequence Quality Scores 
^^^^^^^^^^^^^^^^^^^^^^^^^^^

Sequence quality scores originated form the phred base caller used for Sanger sequencing:



-   Quality = log-transformed error probability

+-----------------------------------+-----------------------------------+
| -   Quality value                 | -   Error probability             |
+-----------------------------------+-----------------------------------+
| -   20                            | -   1/100                         |
| -   30                            | -   1/1000                        |
| -   40                            | -   1/10000                       |
+-----------------------------------+-----------------------------------+

-   Example:

 

1

Open a new terminal (&lt;left windows key&gt;+&lt;t&gt;)

Connect to your VM via ssh using the generated key

-   ssh -i cws.key <ubuntu@134.176.27.XYZ>

ssh might ask for accepting host key on first connect

You will end up with a command prompt within the VM:

Hands on: access VM

 

1

-   Go to Project → Compute → Volumes
-   Click on “Create Volume”

1.

2.

Hands on: volumes

 

-   Choose a name for the volume, e.g. “css\_vol”
-   Select a suitable size (default of 1 GB should be OK now)
-   Click on “Create Volume”

Hands on: volumes

 

-   Volume was created and is available now
-   Extend menu for volume and select “Manage Attachments”

Click on arrow to extend menu

Hands on: volumes

 

-   Select the instance we have created before
-   Click on “Attach Volume”

1.

2.

Hands on: volumes

 

-   Volume list now shows the volume as attached
-   No more fancy feedback in dashboard…
-   … but in the VM!

Hands on: volumes

 

VM

PUB

PRIV

Project

Domain

Floating IP

Network

Flavor

Images

SSH Keys

PUB

Volume

Router

Dashboard

Internet

Cloud

User

-   VM disks are not persistent
-   Data on these disks is lost if VM is deleted
-   Volumes are persistent
-   Attachable to VMs
-   Use for persistent data or data transfer
-   Owned by project

Hands on: volumes

 

-   Switch back to the terminal running ssh (or restart it)
-   Invoke “*ls /dev/vd\”
-   New block device vdb appeared
-   Create a filesystem on it: “*sudo mkfs.ext4 /dev/vdb*”
-   “mount” it: “*sudo mount /dev/vdb /mnt*”
-   Validate with e.g. “*df*” command:

Hands on: volumes

 

1

Volume is now accessible as standard file system

Can be detached and attached to other VMs

Stays around until being deleted

But:

-   Volumes only accessible within project
-   Choice of file system depends on operating system
-   Might require management of posix users/groups
-   Not a shared file system, attachable to one VM at a time
-   Durability / accessibility depends on site setup

Hands on: volumes

Prepare Working Directory 
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

Part I: Data Pre-Processing

cd

mkdir /mnt/workdir

ln -s /mnt/workdir

Create workdir
--------------

cd \~/Data/

cp -r raw\_data \~/workdir/



Copy data
---------

-   Ensure everyone has equally structured FS
-   Keep results in volume (crash safe)

FastQC 
^^^^^^^^^=

-   FastQC graphical quality control tool
-   Accepts FASTQ, SAM, BAM
-   Results exportable

Part I: Data Pre-Processing

fastqc --help



Run FastQC
----------

-   FastQC graphical quality control tool
-   Accepts FASTQ, SAM, BAM
-   Results exportable

FastQC 
^^^^^^^^^=

-   FastQC graphical quality control tool
-   Accepts FASTQ, SAM, BAM
-   Results exportable

Part I: Data Pre-Processing

fastqc --help



Run FastQC
----------

cd \~/workdir

mkdir -p \~/www/FastQC/BGA1\_1\_R1

mkdir -p \~/www/FastQC/BGA1\_1\_R2



fastqc -t 16 -o \~/www/FastQC/BGA1\_1 raw\_data/BGA1\_1\_R1.fastq



fastqc -t 16 -o \~/www/FastQC/BGA1\_1 raw\_data/BGA1\_1\_R2.fastq



Create reports
--------------

FastQC 
^^^^^^^^^=

-   FastQC graphical quality control tool
-   Accepts FASTQ, SAM, BAM
-   Results exportable

Part I: Data Pre-Processing

fastqc --help



Run FastQC
----------

-   Open http://YOUR\_OPENSTACK\_INSTANCE\_IP/
-   We will inspect the report together now ...

cd \~/workdir

mkdir -p \~/www/FastQC/BGA1\_1\_R1

mkdir -p \~/www/FastQC/BGA1\_1\_R2



fastqc -t 16 -o \~/www/FastQC/BGA1\_1\_R1
raw\_data/BGA1\_1\_R1.fastq



fastqc -t 16 -o \~/www/FastQC/BGA1\_1 \_R2
raw\_data/BGA1\_1\_R2.fastq



Create reports
--------------

Quality Treatment 
^^^^^^^^^^^^^^^^^^^^^

Reads contain errors (0.1-15%) and contamination

Quality matters!?

-   NGS high throughput = lots of data
-   The more data the more errors (systematic errors)
-   16S data &gt; WGS read-based &gt; WGS assembly-based
-   Better data = lower computational cost
-   Decrease of false positives
-   But: always trade-off (false negatives)

Quality based filtering vs error correction

Many many tools available

Part I: Data Pre-Processing

Quality Treatment 
^^^^^^^^^^^^^^^^^^^^^

Part I: Data Pre-Processing

Typical workflow:

de-multiplex

merge reads

clip adapters

trim by quality

filter by length

clip primers

raw data

high quality data

16S based

WGS read-based

WGS assembly-based

Quality Treatment 
^^^^^^^^^^^^^^^^^^^^^

Part I: Data Pre-Processing

Typical workflow:

de-multiplex

merge reads

clip adapters

trim by quality

filter by length

clip primers

raw data

high quality data

16S based

WGS read-based

WGS assembly-based

-   Only 16S data
-   Merge: FLASh
-   Clip primers: cutadapt
-   Trim quality: sickle
-   Filter length: ea-utils

For this exercise:

Quality Treatment – Merge Reads 
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

Part I: Data Pre-Processing

Assembly of forward and reverse read pairs

-   If original DNA fragment short than 2x read length

Ungapped alignment with *min overlap* region (favors Illumina)

Quality scores at merged positions recalculated (abs difference)

Quality Treatment – Merge Reads 
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

Part I: Data Pre-Processing

cd \~/workdir/raw\_data

flash BGA1\_1\_R1.fastq BGA1\_1\_R2.fastq -r 300 -o BGA1\_1







Merge reads
-----------

Assembly of forward and reverse read pairs

-   If original DNA fragment short than 2x read length

Ungapped alignment with *min overlap* region (favors Illumina)

Quality scores at merged positions recalculated (abs difference)

Quality Treatment – Merge Reads 
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

Part I: Data Pre-Processing

cd \~/workdir/raw\_data

flash BGA1\_1\_R1.fastq BGA1\_1\_R2.fastq -r 300 -o BGA1\_1







Merge reads
-----------

Assembly of forward and reverse read pairs

-   If original DNA fragment short than 2x read length

Ungapped alignment with *min overlap* region (favors Illumina)

Quality scores at merged positions recalculated (abs difference)

Quality Treatment – Merge Reads 
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

Part I: Data Pre-Processing

cd \~/workdir/raw\_data

flash BGA1\_1\_R1.fastq BGA1\_1\_R2.fastq -r 300 -o BGA1\_1







Merge reads
-----------

Assembly of forward and reverse read pairs

-   If original DNA fragment short than 2x read length

Ungapped alignment with *min overlap* region (favors Illumina)

Quality scores at merged positions recalculated (abs difference)

-   Adjust min/max overlap as necessary
-   Provide fragment length and SD if available

Quality Treatment – Primer Clipping 
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

Part I: Data Pre-Processing

cd \~/workdir/raw\_data



cat Primers.txt



cutadapt -g \^CTACGGGNGGCWGCAG BGA1\_1.extendedFrags.fastq -o
BGA1\_1.f\_tr.fastq -e 0.2 -O 10 --untrimmed-output
BGA1\_1.f\_utr.fastq





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




