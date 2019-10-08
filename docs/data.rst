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
  wget https://openstack.cebitec.uni-bielefeld.de:8080/swift/v1/mgcourse_data/16Sdata.tgz
  
Then, unpack the tar archive::

  tar -xzvf 16Sdata.tgz
  
  or
  
and remove the tar archives::

  rm 16Sdata.tgz
  
Have a short look, on what is contained within the data directory. It should look like this::

  ubuntu@sebmain-939e0:~/workdir/16Sdata$ ls -al ~/workdir/16Sdata
  total 203412
  drwx------ 2 ubuntu ubuntu     4096 Oct  7 15:03 .
  drwxr-xr-x 4 ubuntu ubuntu     4096 Oct  8 04:52 ..
  -rw------- 1 ubuntu ubuntu 37091989 Oct  7 14:56 057_R1.fastq
  -rw------- 1 ubuntu ubuntu 37091989 Oct  7 14:56 057_R2.fastq
  -rw------- 1 ubuntu ubuntu 27836957 Oct  7 14:56 058_R1.fastq
  -rw------- 1 ubuntu ubuntu 27836957 Oct  7 14:56 058_R2.fastq
  -rw------- 1 ubuntu ubuntu 19458566 Oct  7 14:54 068_R1.fastq
  -rw------- 1 ubuntu ubuntu 19458566 Oct  7 14:54 068_R2.fastq
  -rw------- 1 ubuntu ubuntu 19746613 Oct  7 14:53 074_R1.fastq
  -rw------- 1 ubuntu ubuntu 19746613 Oct  7 14:53 074_R2.fastq
  -rw------- 1 ubuntu ubuntu       67 Oct  7 14:57 primers.txt
  ubuntu@sebmain-939e0:~/workdir/16Sdata$ 

If you want to disable system beep sounds::

  xset -b
  
Activate Conda Environment
^^^^^^^^^^^^^^^^^^^^^^^^^^

In order to follow the hands-on session you need to activate a special conda environment where all tools and software has been installed and is available::

  conda activate mgcourse
  
Note: You need to do this in every terminal!
