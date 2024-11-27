# Research Cluster Instructions

## Connecting to the Cluster

1. **Accessing the Cluster**: 
   To begin, ensure you have access to the Discovery cluster(if not, please apply for one at [rc request](https://service.northeastern.edu/tech?id=sc_cat_item&sys_id=0ae24596db535fc075892f17d496199c)). You can log in by using the SSH command: 
   ```
   ssh <your_username>@login.discovery.neu.edu
   ```
   You will then be prompted to enter your email password. Once logged in, you will be directed to your home directory`$Home`, located at `/home/<your_username>`. Please be aware that this is a login node, and extensive computational tasks should not be performed here. Instead, you will need to request a compute node to train and test your code.

2. **Recommended SSH Login Method**: 
   The preferred(and **RECOMMENDED**) method for logging into Discovery is via SSH. First, generate an SSH key, then add your `id_rsa.pub` file to the `authorized_keys` file in discovery. For detailed instructions, refer to the guide for [Mac](https://rc-docs.northeastern.edu/en/latest/connectingtocluster/mac.html). Once set up correctly, you will be able to log in without needing to enter your password each time.

**Note**: It is not recommended to use VSCode for connecting to Discovery. Since the VSCode Server is not integrated into Discovery, it may take a significant amount of time to initialize VSCode each time you log in, as well as increase CPU usage on the login node.

**Note**: Additionally, using Open OnDemand (OOD) terminal for connecting to Discovery is also discouraged due to poor connection reliability experienced while using OOD.

**Note**: If anyone finds a good solution to these connection problems, please let me know so we can adopt a better approach!

## Basic Environment Setup

1. **Requesting a Compute Node**  
   To request a compute node, use the following `srun` command syntax:
   ```bash
   srun [options] [command]
   ```
   **Example**:
   ```bash
   srun --partition=gpu --nodes=1 --gres=gpu:v100-sxm2:1 --cpus-per-task=2 --mem=10GB --time=02:00:00 --pty /bin/bash
   ```
   - `--partition` specifies the node type, such as `gpu`.
   - `--gres` specifies the type of GPU requested, in this case, a `v100-sxm2`.
   - `/bin/bash` starts a bash shell on the compute node.

2. **Conda Setup**  
   After you access the compute node, load the required versions of Anaconda and CUDA:
   ```bash
   module load anaconda3/2022.05 cuda/11.8
   ```
   Then, create and activate a Conda environment, and install packages as you would on your local machine:
   ```bash
   conda create --prefix=<env_path> -c conda-forge python=3.10 -y
   conda activate <env_path>
   conda install <package>
   ```

3. **Where to Store Environments and Data**  
   You have three main directories for storing environments and data:
   - `/scratch/<your_username>`: Temporary storage, though files are not frequently cleared.
   - `/home/<your_username>`: Temporary storage we suppose, though documentation is unclear about usage limits.[ins](https://rc-docs.northeastern.edu/en/latest/datamanagement/discovery_storage.html)
   - `/work/<groupname>`: Permanent storage. You must request access by submitting a [storage request](https://service.northeastern.edu/tech?id=sc_cat_item&sys_id=891235d31b20c0502dafc8415b4bcb0e).

4. **Next Time Login**
    First request a compute node, and then load the anaconda(*perhaps you don't need*) and actiavte the conda env. You can also check exsited env using
    ```bash
    conda env list
    ```

## 3. **Running Jupyter Notebooks using OOD**
   After accessing the compute node and loading the required environment of Conda, run the following command to install JupyterLab:
```
   conda install jupyterlab -y
```
Next, open Open OnDemand (OOD) by referring to the [open ondemand guide](https://rc.northeastern.edu/ood/#:~:text=Log%20in%20to%20OOD.,launching%20JupyterLab%20Notebook%20will%20appear.) 
In the dropdown menu for interactive apps, select `JupyterLab Notebook`. Configure the environment by specifying the CUDA version and Conda environment (full path required), as shown in the screenshot below: 

<img width="640" alt="Screenshot 2024-11-06 at 15 21 08" src="https://github.com/user-attachments/assets/1e0a6a0c-2e18-4c06-b2b9-c5825d22eb84"> 

Finally, click Launch and wait for the session to initialize.


Now, you’re ready to make full use of the Research Cluster!

--- 

Let me know if anyone find something helpful! I'll keep updating it!

## A quick look at how to use sbacth to submit jobs
First you need to create a `.sh` file, let's call it `smartgd.sh`:

```sh
#!/bin/bash

#SBATCH --nodes=1
#SBATCH --time=04:00:00
#SBATCH --job-name=smartgd
#SBATCH --mem=48G
#SBATCH --partition=gpu
#SBATCH --gres=gpu:a100:1
#SBATCH --ntasks=1
#SBATCH --cpus-per-task=2
#SBATCH --error=%A-%a.err
#SBATCH --output=%A-%a.out
#SBATCH --array=1-10%1  #execute 2 array jobs, 1 at a time.


# Your program/command here

module load anaconda3/2022.05
module load cuda/11.8
source activate /scratch/li.xuefen/gd_test

cd /scratch/li.xuefen/SmartGD
python3 -c "import torch; print(torch.__version__)"
python3 run.py

```

then in your command line(this can be done at login nodes):
```bash
sbatch smartgd.sh
```

to have a view on your jobs
```
squeue -u <your username>
```
to cancel all your jobs
```
scancel -u <your username>
```
