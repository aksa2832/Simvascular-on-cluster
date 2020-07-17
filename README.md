# Simvascular-on-cluster
Files and resources to :
1. Build Simvascular's svpre, svpost, svsolver on a supercomputing cluster. 
2. Submit a simulation job using svsolver.
3. Check the progress of your simulation
4. Download the generated files after the simulation is completed

> Note : the following instructions have been written for use on University of Colorado Boulder's research computing account. 
> Please consult your supercomputing facility if you are affiliated with a different cluster, as some steps might be unique for your cluster account system.

## 1. To build a container for use on the cluster
You can directly pull this [![https://www.singularity-hub.org/static/img/hosted-singularity--hub-%23e32929.svg](https://www.singularity-hub.org/static/img/hosted-singularity--hub-%23e32929.svg)](https://singularity-hub.org/collections/4568) ready built container onto your supercomputing cluster account's project directory (the directory intended to store software builds and smaller data sets) by: 
  - Login to your cluster account
```sh
  $ cd projects/$USER/
  $ mkdir svsolver-container-build
  $ cd svsolver-container-build
  $ singularity pull simvascular-svsolver.sif shub://aksa2832/Simvascular-on-cluster
  ```
**OR**

  - Create a public github repository and copy the 3 build here files to your github repo. 
    Build files for CU Boulder's supercomputing cluster: quick-build-linux.sh, quick-build-centos7-open-mpi.sh, Singularity (text file containing recipe for the build)
  - Then, login to https://singularity-hub.org with your github credentials.
  - Go to **My collections** -> **Add collection** -> choose your new github repository with the build files and enter the recipe file name as **Singularity**.
    This will automatically build the container for you and place it on singularity hub. Here is how it should be after build: [![https://www.singularity-hub.org/static/img/hosted-singularity--hub-%23e32929.svg](https://www.singularity-hub.org/static/img/hosted-singularity--hub-%23e32929.svg)](https://singularity-hub.org/collections/4568)
  - Download the container to your supercomputing cluster account's project directory (the directory intended to store software builds and smaller data sets) by:
  
  ```sh
  $ cd projects/$USER/
  $ mkdir svsolver-container-build
  $ cd svsolver-container-build
  $ singularity pull simvascular-svsolver.sif shub://<yourgithubusername>/<svsolver_github_reponame>
```
------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------
## 2. To submit a job: Prepare data-folder and job-script
  Step 1: Create the data files for the job with **Simvascular application** on your local computer. 
  - Open SimVascular
  - Right click the job node (eg. "test-simvascular-project") under **Simulations** in Data Manager
  - Click "Export Data Files"
  - Select a folder for exporting(eg. "test-simvascular-project-data"). 
  
  Step 2: Write and load a solver script.
  - Write the script: Follow instructions given in the template file "sample_jobscript" and modify the parameters according to your requirements.
  - Save the script as your-job-script.sh to the folder containing the exported data files from SimVascular after step 1("test-simvascular-project-data") .
  This folder should then contain all the Data Files and the job script required for the simulation.
  
  Step 3: Upload this data-folder on your supercomputing cluster account's working directory(usually /scratch/$USER).
   - Open terminal(or equivalent command prompt).
   - Secure copy the data-folder("test-simvascular-project-data") onto the cluster's working directory. Here <username>@login.rc.colorado.edu is the login id you use for cluster account.
   ```sh
   $ scp -rv </path/to/test-simvascular-project-data/on/your/computer> <username>@login.rc.colorado.edu:</target/path/to/working/directory/on/cluster/>
   ```

## 2. Submit the job: 

   Step 4: Submit the simulation job.
   - Login to your cluster account
   - navigate to the working directory(eg. /scratch/$USER/test-simvascular-project-data) from where the simulation will be launched.
   - Enter the following commands to submit the job:
   ```sh
   $ module load /slurm/summit
   $ sbatch your-job-script.sh
   ```
   
------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------
## 3. Check the progress of your simulation
  - If you have enabled email notification in you job script, you will be notified as soon as the simulation starts and the moment it ends via. email.
  - Find information on your job’s start time using the squeue command:
  ```sh
  $ squeue --user=your_rc-username --start
  ```
  - To check the status/progress of the submitted job
  ```sh
  $ sacct --starttime=YYYY-MM-DD --jobs=your_job-id --format=User,JobName,JobId,partition,state,time,start,end,elapsed,MaxRss,MaxVMSize,nnodes,ncpus,nodelist

  ```
  - Finally check the output file(.out file) generated in </scratch/$USER/test-simvascular-project-data> for simulation performance details. 
   
------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------
## 4. Download generated files 
  - Compress the folder containing generated files(will be named n-procs_case for n cores used):
  ```sh
  $ tar -cvzf <<n-cores>-procs_case>.tar.gz </scratch/$USER/test-simvascular-project-data/<n-cores>-procs_case>
  ```
  - Use globus file transfer or secure copy the zipped folder on your local computer:
  ```sh
  scp -rv <username>@login.rc.colorado.edu:</scratch/$USER/test-simvascular-project-data/<n-cores>-procs_case.tar.gz> <target/storage/path/on/your/computer> 
  ```
  
