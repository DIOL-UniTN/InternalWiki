# Slurm short guide
## What is it
!!! quote "From slurm website"
    Slurm is an open source, fault-tolerant, and highly scalable cluster management and job scheduling system for large and small Linux clusters. As a cluster workload manager, Slurm has three key functions. First, it allocates exclusive and/or non-exclusive access to resources (compute nodes) to users for some duration of time so they can perform work. Second, it provides a framework for starting, executing, and monitoring work (normally a parallel job) on the set of allocated nodes. Finally, it arbitrates contention for resources by managing a queue of pending work.

Choosing Slurm just makes sense. It's not just a workload manager; it's your partner in getting stuff done efficiently. Imagine a world where you don't have to worry about resource chaos and can navigate through your computational tasks effortlessly. Slurm not only streamlines your workflow but also empowers you to make the most out of your computing infrastructure. So why settle for anything less? Make Slurm your ally, and let's take your high-performance computing experience to the next level!

## How to use it
### Step 1: Understanding the Basics
Before diving in, familiarize yourself with the fundamental concepts of Slurm:

- **Job**: A unit of work scheduled by Slurm.
- **Partition**: A grouping of computing resources, allowing you to allocate tasks strategically.
- **Node**: An individual computer in the cluster.

### Step 2: Checking the Cluster Status

To see the current status of the cluster, use the following command:

```bash
sinfo
```

This will display information about partitions, nodes, and their availability.

### Step 3: Submitting a Job
Prepare a script containing your computational tasks, for example, a bash script named `my_script.sh`. Then, submit it to Slurm using:

```bash
sbatch my_script.sh
```

An example of the bash script could be:

```bash linenums="1"
#!/bin/bash
#SBATCH -c 1
#SBATCH -n 10
#SBATCH -t 150:00:00
#SBATCH -p LocalQ
#SBATCH -o /home/your_user/my_folder/runs/test.out 
#SBATCH -e /home/your_user/my_folder/error_logs/test_error.out

source my_venv/bin/activate
cd my_folder/ 
python3 main.py
```

Here below the explanation of the script, for more info follow the slurm [official page](https://slurm.schedmd.com/sbatch.html):

- **Line 2**: Advise the Slurm controller that ensuing job steps will require *ncpus* (in this case 1) number of processors per task. Without this option, the controller will just try to allocate one processor per task.
- **Line 3**: This option advises the Slurm controller that job steps run within the allocation will launch a maximum of *number* (in this case 10) tasks and to provide for sufficient resources.
- **Line 4**: Set a limit on the total run time of the job allocation. If the requested time limit exceeds the partition's time limit, the job will be left in a PENDING state (possibly indefinitely). The default time limit is the partition's default time limit.
- **Line 5**: Request a specific partition for the resource allocation. If not specified, the default behavior is to allow the slurm controller to select the default partition as designated by the system administrator.
- **Line 6**: Instruct Slurm to connect the batch script's standard output directly to the file name specified
- **Line 7**: Instruct Slurm to connect the batch script's standard error directly to the file name specified
- **Line 8-10**: Actual bash script

### Step 4: Monitoring Jobs

Monitor all jobs using:
```bash
squeue
```
or your jobs:
```bash
squeue -u your_username
```

This will show the status of your submitted jobs.

### Step 5: Canceling a Job

To cancel a job, simply use:

```bash
scancel <your_job_id>
```

Replace `<your_job_id>` with the ID of the job you want to cancel.

## Submit your first job

Here a tutorial to submit your first job! Connect to the server and start the tutorial.

### Create python script
Create a very simple python script in a custom folder `my_folder` and call it `my_py_script.py`:

```bash
mkdir my_folder
cd my_folder
nano my_py_script.py
```

copy and paste this toy code:

```py title="my_py_script.py"
import time

time.sleep(60)
print("Hello world!")
```
!!! note
    Note that the script wait 1 minute in order to let you have the time to see it in the queue

Then go back to your home and create a bash script and call it `my_bash.sh`

```bash
cd ..
nano my_bash.sh
```

copy and paste this code:

```bash
#!/bin/bash
#SBATCH -o /home/your_username/my_folder/test.out
#SBATCH -e /home/your_username/my_folder/error_test.out

python3 my_folder/my_py_script.py
```

Now submit your bash script to slurm:

```bash
sbatch my_bash.sh
```

And monitor it in the queue:

```bash
squeue
```

After a short interval (approximately 1 minute, taking into account the execution time of the Python script), you will observe the appearance of the phrase "Hello world!" in the `test.out` file. Check it with:

```bash
cat my_folder/test.out
```

Congrats, you've submitted and monitored your first slurm job!