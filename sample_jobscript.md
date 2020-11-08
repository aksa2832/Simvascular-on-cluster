#!/bin/bash

#SBATCH --nodes=1
#SBATCH --ntasks=24
#SBATCH --time=24:00:00
#SBATCH --mail-type=ALL
#SBATCH --mail-user=<username>@colorado.edu
#SBATCH --partition=shas
#SBATCH --output=sample.%j.out  


## load needed modules
module purge
export MODULEPATH=/curc/sw/modules/spack/spring2020/linux-rhel7-x86_64/Core:$MODULEPATH
module load gcc/8.4.0
module load openmpi/2.1.6
module load singularity/3.6.4

## run the solver script 
## note that you'll need to modify the path to the container to point to its actual location eg. /path/to/ : /projects/$USER/svsolver-container-build
## note also it is assumed that your working directory is under scratch directory eg. "/scratch/summit/$USER/test-simvascular-project"
## test-simvascular-project is the directory that contains "solver.inp" ; modify accordingly

## cd /scratch/summit/$USER/test-simvascular-project

mpirun -n $SLURM_NTASKS singularity exec --bind /scratch/summit/$USER /path/to/simvascular.sif /opt/svSolver/BuildWithMake/Bin/svsolver-openmpi.exe solver.inp

## note also you can run svpre and svpost with the container (where "<args>" is the arguments you 
## need to give to svpre or svpost); these programs don't require mpirun: 

## singularity exec /path/to/simvascular.sif /opt/svSolver/BuildWithMake/Bin/svpre.exe <args>
## singularity exec /path/to/simvascular.sif /opt/svSolver/BuildWithMake/Bin/svpost.exe <args>
