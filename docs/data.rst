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

  ls -l ~/workdir/data/
  -rw-r--r-- 1 ubuntu ubuntu 4372654 Aug 30 08:24 Reference.fna
  drwxr-xr-x 2 ubuntu ubuntu   24576 Aug 30 08:24 fast5
  drwxrwxr-x 2 ubuntu ubuntu    4096 Sep  5 07:23 fast5_small
  drwxrwxr-x 2 ubuntu ubuntu    4096 Sep 12 08:01 fast5_tiny
  drwxr-xr-x 2 ubuntu ubuntu    4096 Aug 30 08:36 illumina

If you want to disable system beep sounds::

  xset -b
