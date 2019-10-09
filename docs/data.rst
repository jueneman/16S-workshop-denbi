The Tutorial Data Set
================================

The first thing you need to do is to connect to your virtual machine with the X2Go Client. If you are working with your laptop and haven't installed it yet - you can get it here:
https://wiki.x2go.org/doku.php/download:start

Enter the IP of your virtual machine, the port, the username "ubuntu" and select your ssh key. When you have successfully connected to your machine, open a terminal.

As you have started the VM with a volume attached, this volume needs to be given to the ubuntu user for easy access::

  sudo chown ubuntu:ubuntu /mnt/volume/
  
Create a link in your home directory to the mounted volume::

  ln -s /mnt/volume/ workdir 

The tutorial dataset is located in our object store. We have also prepared some precomputed results.You can get both here::

  cd ~/workdir
  wget https://openstack.cebitec.uni-bielefeld.de:8080/swift/v1/mgcourse_data/16S-data.tgz
  
Then, unpack the tar archive::

  tar -xzvf 16S-data.tgz
  ln -s 16S-data/raw/ 16Sdata
  
and remove the tar archives::

  rm 16Sdata.tgz
  
Have a short look, on what is contained within the data directory. It should look like this::

  (mgcourse) ubuntu@sebmain-939e0:~/workdir$ ls -la 16Sdata/
  total 179704
  drwxrwxr-x 2 ubuntu ubuntu     4096 Oct  8 12:12 .
  drwxr-xr-x 4 ubuntu ubuntu     4096 Nov 20  2015 ..
  -rw-rw-r-- 1 ubuntu ubuntu  5712157 Nov 20  2015 BGA1_1_R1.fastq
  -rw-rw-r-- 1 ubuntu ubuntu  5712157 Nov 20  2015 BGA1_1_R2.fastq
  -rw-rw-r-- 1 ubuntu ubuntu 21370972 Nov 20  2015 BGA1_2_R1.fastq
  -rw-rw-r-- 1 ubuntu ubuntu 21370972 Nov 20  2015 BGA1_2_R2.fastq
  -rw-rw-r-- 1 ubuntu ubuntu  8910118 Nov 20  2015 BGA2_1_R1.fastq
  -rw-rw-r-- 1 ubuntu ubuntu  8910118 Nov 20  2015 BGA2_1_R2.fastq
  -rw-rw-r-- 1 ubuntu ubuntu 11004978 Nov 20  2015 BGA2_2_R1.fastq
  -rw-rw-r-- 1 ubuntu ubuntu 11004978 Nov 20  2015 BGA2_2_R2.fastq
  -rw-rw-r-- 1 ubuntu ubuntu 19342508 Nov 20  2015 BGA3_1_R1.fastq
  -rw-rw-r-- 1 ubuntu ubuntu 19342508 Nov 20  2015 BGA3_1_R2.fastq
  -rw-rw-r-- 1 ubuntu ubuntu 12128141 Nov 20  2015 BGA3_2_R1.fastq
  -rw-rw-r-- 1 ubuntu ubuntu 12128141 Nov 20  2015 BGA3_2_R2.fastq
  -rw-rw-r-- 1 ubuntu ubuntu  9294674 Nov 20  2015 BGA4_1_R1.fastq
  -rw-rw-r-- 1 ubuntu ubuntu  9294674 Nov 20  2015 BGA4_1_R2.fastq
  -rw-rw-r-- 1 ubuntu ubuntu  4222729 Nov 20  2015 BGA4_2_R1.fastq
  -rw-rw-r-- 1 ubuntu ubuntu  4222729 Nov 20  2015 BGA4_2_R2.fastq
  -rw-r--r-- 1 ubuntu ubuntu     1240 Nov 23  2015 Pipe.tsv
  -rw-rw-r-- 1 ubuntu ubuntu       55 Nov 20  2015 Primers.txt
  (mgcourse) ubuntu@sebmain-939e0:~/workdir$ 

  If you want to disable system beep sounds::

  xset -b
  
Activate Conda Environment
^^^^^^^^^^^^^^^^^^^^^^^^^^

In order to follow the hands-on session you need to activate a special conda environment where all tools and software has been installed and is available::

  conda activate mgcourse
  
Note: You need to do this in every terminal!
