# Using MPI with python on HPC

  * Follow the instructions in readme and get conda running
  * Install mpi4py using `conda install mpi4py`
  * Run any python command using
  
    `mpiexec -n <number of processes> /share/<unityid>/miniconda/bin/python2.7 <script_name>.py`
