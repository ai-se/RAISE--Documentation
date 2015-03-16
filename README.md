# A Quick guide to using our HPC Clusters
## Installing Python2.7: 
1. Login to the HPC cluster using ```ssh -X rkrish11@login64.hpc.ncsu.edu```
2. Navigate to `/share` (or `/share2` or `/share3`) and create a folder. __Note: Use your unity ID as the directory name to avoid filename conflicts.__
3. Now go into your folder and download the latest version of [miniconda](http://conda.pydata.org/miniconda.html): 
  
  ```wget http://repo.continuum.io/miniconda/Miniconda-latest-Linux-x86_64.sh```
  and install it ```bash Miniconda-latest-Linux-x86_64.sh```. __Note: You've gotta install this in your share space. Watch out for the part during the installation when it asks where to install this, say `./miniconda` instead on the default which is `~/miniconda`__
4. These Miniconda installers contain the 'conda' package manager. Once Miniconda is installed, you can use the conda command to install any other packages and create environments, etc. But to do this you must export this to your path.
### Add Miniconda to your path
1. Go back to home directory; create a file called `.tcshrc'; add these to the file:
  ```set path= ($path /share/rkrish11/miniconda/bin/ $home/bin/)```

