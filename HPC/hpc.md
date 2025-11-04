# Understanding the HPC clusters

##### *Funny Joke Incoming* 

---

## **What is a HPC?**

HPC stands for **High Performance Computing** refers to the practice of aggregating computing power which allows us to deliver higher performnance than one could get out of a typical local desktop or laptop to solve large problems. Quite often, the resources such as file systems, CPUs, and GPUs for computational power are shared among many individuals.

### **Why should we use a HPC?**

- Accelerate workflow by using multiple CPU cores: Typically a standard laptop/desktop has multiple CPUs (~8-16), however, HPC clusters may consist of much higher number of CPUs (e.g. up to 112 CPUs)
- Run multiple tasks in parallel: Commonly in bioinformatics, we have multiple samples which runs the same pipeline (e.g. alignment). Without the HPC, we would need multiple laptops or desktops to run each sample, but in the HPC, you could in theory run both or multiple samples in parallel.

### **What are Nodes?**

Nodes refer to the indiviudal computers that compose a HPC cluster, and on the cluster there will be different types of nodes for different types of tasks.
Similar to your own laptop, each node has CPUs (processors or cores), memory (RAM) and disk space. **CPUs** are the computational tool for running programs and calculations, nd the information about the current task is stored in the computer's memory. The disk refers to all the storage which while can be local, it is common for the nodes to be connected to a shared, remote fileserver.

![node](https://raw.githubusercontent.com/jaoseph/JBLab-BioinformaticsWiki/main/pictures/node_anatomy.png)

#### **Login Nodes**
When you enter into the HPC, you typically arrive at the **Login Node** which serves as the acess point to the cluster. The login node can also be referred to as *login node* and *submit node*

**Importantly, the login node should not be used for time-consuming or resource-intensive tasks.**

#### **Compute Nodes**
The **computing nodes** are where the computational work gets done, and may come in many differnet shapes and sizes. Generally, they are dedicated to long and resource-intensive tasks that require a lot of computational resources.

---

### **Job Scheduler**

Quite often, a HPC system may have thousands of nodes and users. How does the system decide who gets what resources and when a task is ran?

This is where the **Job Scheduler** software comes in (in CRUK CI, this is **Slurm**). The Job Scheduler is akin to a waiter at a busy restaurant whereby you have to queue for a popular restaurant and you are allocated a seat (running a job) based on how many people (tasks) and what tables (nodes) are free. In computational term, this means it manages and allocates computational resources to ensure fair resource shraing, efficient utilization of the compute nodes. This takes in as a form monitoring jobs, queuing jobs, evaluating resource needs such as memory (RAM), CPUs, task time.

![job_scheduler](https://raw.githubusercontent.com/jaoseph/JBLab-BioinformaticsWiki/main/pictures/restaurant_queue_manager.svg)

### Batch Job Submission

The most basic use of the Job Scheduler is to run a command non-interactively (in a compute node). Anything from a single command to a whole script that you want to run on the cluster non-interactively are called a **job**, and the process of using the Job Scheduler to run your job is known as **Batch Job Submision**. 

#### Let's go through some basic job submission lingo and examples

#### Slurm Key Commands


| Command | Meaning / Equivalent | Example |
|------|-----------------------|---------|
| sbatch | Submit a shell script to the queue | `sbatch slurm-submit.sh` |
| squeue | see all the jobs in the squeue (everyone) | `squeue` |
| squeue -u USERNAME | see all the jobs in the squeue (everyone) | `squeue -u chong02` |
| scancel JOBID | Cancel the job with the specified ID | `scancel 44347166` |
| scancel -u USERNAME | Cancel all the jobs under your name | `scancel -u chong02` |
| seff JOBID | Get efficiency information about your job |  |



--- 

### **File System**

Quite often, the HPC is split into **home** file-system and **scratch** file-system. 

The home directory (```/home/username/``` or ```/users/username/```) are small storage spaces that are frequently backed up by the HPC system. This is intended for long-term stable storage for **small** scripts, source codes and datasets. These includes some job submission scripts (```.sh``` or ```.sbatch```) and conda environments. 

The scratch directory (```/scratch/```) are large storage spcaes that are **NOT** backed up by th HPC system. Imagine the scratch directory as a scratch paper for art. Most of your intensive workload final and temporary files will be located here during and following job runs. Once a file here is deleted or the scratch server goes down, ALL your data will be lost permanently. Make sure that files that are important which should be backed up are also in another file system (NOT HOME DIRECTORY)
