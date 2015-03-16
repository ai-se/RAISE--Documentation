# A Quick guide to Setting Up the HPC Clusters
## Installing Python2.7: 
1. Login to the HPC cluster using ```ssh -X rkrish11@login64.hpc.ncsu.edu```
2. Navigate to `/share` (or `/share2` or `/share3`) and create a folder. __Note: Use your unity ID as the directory name to avoid filename conflicts.__
3. Now go into your folder and download the latest version of [miniconda](http://conda.pydata.org/miniconda.html): 
  
  ```wget http://repo.continuum.io/miniconda/Miniconda-latest-Linux-x86_64.sh```
  and install it ```bash Miniconda-latest-Linux-x86_64.sh```. __Note: You've gotta install this in your share space. Watch out for the part during the installation when it asks where to install this, say `./miniconda` instead on the default which is `~/miniconda`__
4. These Miniconda installers contain the 'conda' package manager. Once Miniconda is installed, you can use the conda command to install any other packages and create environments, etc. But to do this you must export this to your path.

### Add Miniconda to your path and installing python packages using `conda`
1. Go back to home directory; create a file called `.tcshrc`; 
   Add this line to the file:
   ```set path= ($path /share/rkrish11/miniconda/bin/ $home/bin/)```
2. An optional (but strogly recommended) step would be to create an alias for python2.7 by adding the following lines to .tcshrc `alias python "python2.7"`
3. Logout and log back if you already haven't done so. Now you should be able to install all the packages using `conda install <package-name>`

## Installing git
1. Go to your home directory and create and directory called tmp. cd to tmp and download the git 2.3.3 tarball using ```wget --no-check-certificate https://www.kernel.org/pub/software/scm/git/git-2.3.3.tar.gz```
2. Untar using ```tar xfvz git-2.3.3.tar.gz``` and cd to git-2.3.3.
3. Run `make` and then run `make install`
4. Git is installed in `$HOME/bin` by default. So add that to your environment variable in `.tcsh`. 
5. Also, you'll need to turn off SSL Verification to be able to run git so add `alias git "env GIT_SSL_NO_VERIFY=true git"` to your `.tcshrc`. 
6. At this point, your `.tcshrc` should look as follows:
```
set path= ($path /share/rkrish11/miniconda/bin/ $home/bin/)
alias git "env GIT_SSL_NO_VERIFY=true git"
alias python "python2.7"
```
Log out and log back in and you're all set.

# Submitting Jobs
1. As an example, I'll use my [run.py](https://github.com/ai-se/Transfer-Learning/blob/master/SOURCE/run.py) file. On an ordinary unix system, you would have to execute `$ python run.py _test ant > log/ant.py` to get it going.
2. To run this on the cluster use 
`bsub -W 60 -n 4 -o ./out/out.%J -e ./err/err.%J python run.py _test ant > log/ant.log`
  - "-W 5" asks for five minutes of time. The job will time out after five minutes if still running.
  - "-n 4" asks for 4 processors.
  - "-o pmonte.out.%J" denotes a file where standard output from the job will be saved.
  - The "-e" line designates a file where standard error output from the job will be saved.
### Muyltiple Jobs 
To submit multiple jobs, like in my case with 12 more projects like ant. Use SHELL scripts to run your job. Create a master shell script as follows
```shell
#! /bin/tcsh

rm err/*
rm out/*
foreach VAR ("ant" "camel" "forrest" "ivy" "jedit" "pbeans" "log4j" "synapse" "velocity" "xalan" "xerces")
  bsub -W 6000 -n 4 -o ./out/$VAR.out.%J -e ./err/$VAR.err.%J python run.py _test "$VAR" > log/"$VAR".log
end
[rkrish11@login02 SOURCE]$
```
