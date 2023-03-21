# Genomics Pipeline Intro

An introduction to the CHPC - specifically, logging onto a cluster and running scripts on compute nodes using the slurm workload manager.

---

# Contents

-   [Objectives](#objectives)
-   [Getting Set Up](#getting-set-up)
-   [Genomic Filetypes](#genomic-filetypes)
-   [Basic Processing Steps](#basic-processing-steps)
-   [Exercise](#exercise)

---

# <a name="objectives"></a>
# Objectives 

- Logon to a CHPC cluster
- Explore the login node
- Submit a batch script

---

# <a name="getting-set-up"></a>
# Getting set up
If you are here as a UTU student taking BIOL 4300, you should do the following:

1.  Login to your [Github](https://github.com/) account.

1.  Fork [this repository](https://github.com/KLab-UT/CHPC-intro), by
    clicking the 'Fork' button on the upper right of the page.

    After a few seconds, you should be looking at *your* 
    copy of the repo in your own Github account.

1.  Click the 'Clone or download' button, and copy the URL of the repo via the
    'copy to clipboard' button. **note: if you have an SSH key with your github account, make sure you select the ```SSH``` tab**

1.  In your terminal, navigate to where you want to keep this repo (you can
    always move it later, so just your home directory is fine). Then type:

        $ git clone the-url-you-just-copied

    and hit enter to clone the repository. Make sure you are cloning **your**
    fork of this repo.

1.  Next, `cd` into the directory:

        $ cd the-name-of-directory-you-just-cloned

1.  At this point, you should be in your own local copy of the repository.

    As you work on the exercise below, be sure to frequently `commit` your work
    and `push` changes to the *remote* copy of the repo hosted on Github.
---

# <a name="introduction"></a>
# Introduction
The CHPC is the Center for High Performance Computing (HPC) owned and operated by the University of Utah. This guide will help you login to the supercomputer and learn some of the basic commands for running scripts. Before using this guide, you should have completed the following:

1. Created a UNID account
1. Used the class token to be added to the CHPC class group
> note: (if you are using the CHPC for research outside of the class, you don't need to use a token. Instead, a CHPC profile will be created for the K-Lab UTU group. Talk to Randy Klabacka about this.

# <a name="logging-on"></a>
# Logging on
```ssh``` stands for 'secure shell'. By using ssh, you establish a secure connection between you and a remote computer. To use ssh for logging into the CHPC, do the following:

```
ssh <your-UNID>@lonepeak.chpc.utah.edu
```
> note: 'your-UNID' is the ID number you obtained from the University of Utah (include the 'u')

This should prompt you to insert your password. Your password is the same as your UNID password. This will take you to your personal directory on the login node for the cluster. Look at your location using the following command:

```
pwd
```

You'll see that you are within a directory named after your UNID. This is your home directory on the login node. Any time you use the '~' alias, you'll see that it is referring to your home directory. Try this out by moving to the root directory and then move back to your home directory:

```
cd /
pwd
cd ~
pwd
```

***IMPORTANT:*** You should only read/create/edit files within your home directory. You shouldn't have access to anything else, but just-in-case I felt I should mention this here.

Within your home directory you can create your own environment with directories and script files. Create a directory within your home directory called 'LonePeakLearner'.

```
mkdir -p ~/LonePeakLearner
```

Now the directory 'LonePeakLearner' is within your home directory. Check it out (make sure you are in your home directory when you do this)

```
ls
```

Now create a bash script within the 'LonePeakLearner' directory that writes the text 'Hello World' to a text file called 'output.txt' (you can do this using a linux terminal text editor [such as vim] or you can use ```echo``` to create it with one line)

```
cd ~/LonePeakLearner
echo 'echo "hello world\n" >> output.txt' > LonePeakLearner.sh
```

You can then run this script as you would on your local computer, but you are doing it on the cluster!

```
bash LonePeakLearner.sh
```

***IMPORTANT:*** You should never run demanding commands from the login node, regardless of the directory you are in. The login node is for editing files, submitting jobs, and analyzing output. Very inexpensive computation is okay (e.g., installing software packages is usually okay), but be careful. If you notice a job is taking a long time to run, you should kill it (using ```cmd+c```). Any computation that requires lots of time/power should be conducted on the compute nodes- not the login nodes. The compute nodes are introduced below. 

The CHPC has multiple cluster for high performance computing. These can all be seen on the [CHPC website](https://chpc.utah.edu/documentation/gettingstarted.php) in the "Accessing the HPC Systems" section. We only have access to the general clusters (not the protected environment), but you can log into any of these using your UNID.


# <a name="creating-a-batch-script"></a>
# Creating a Batch Script

As stated above, when you login to a CHPC cluster, you land at the login node for that cluster. On this node, you should never run programs that are computationally intensive (i.e., require lots of power or long run times). The set cutoff is 15 minutes- and a command takes longer than this, you will be penalized with a 30-minute 'time out' (seriously, you won't be able to submit anything on the cluster for 30 minutes).

You might be asking yourself, "They why the heck are we using the supercomputer if we can't run big jobs? That doesn't sound very super!" Computationally intensive jobs will be submitted from your login node to a compute node. The software on the CHPC that enables these submissions is called 'slurm'. Slurm is a workload manager used for job submission on HPC clusters so that multiple people can access compute nodes for their high-intensity computing needs.

Move to the 'LonePeakLearner' directory you created earlier.

```
cd ~/LonePeakLearner
```

You should have a bash script here called 'LonePeakLearner.sh' that appends 'hello world\n' to a an output file. You can run this script on a compute node by converting your simple script to a batch script as described below. For more detailed instructions, see the [CHPC doc page](https://chpc.utah.edu/documentation/software/slurm.php#submit). To make your batch script this, create a new bash script called 'SlurmNewbie.sh' and add the following lines to the top of the file:

```
#!/bin/sh
#SBATCH --account=utu
#SBATCH --partition=lonepeak
#SBATCH --nodes=1
#SBATCH --ntasks=1
#SBATCH -o slurm-%j.out-%N
#SBATCH -e slurm-%j.err-%N

cd ~/LonePeakLearner
bash LonePeakLearner.sh
```

Every line that begins with ```#SBATCH``` is an option that will be interpreted by slurm. Any line that starts with ```#``` and is not imediately followed with ```SBATCH``` will simply be interpreted as a comment. To see the values you can include for ```account``` and ```partition```, use the ```myallocation``` command below.

```
myallocation
```

The ```--nodes``` (```-N```) option specifies the number of nodes you are requesting. You can think of a node as a single computer. When we refer to the supercomputer as "the cluster", we are referring to the "cluster" of nodes that are used. In other words, the collection of interconnected computers that can be accessed for computing tasks. For computationally intensive jobs, you may want to use multiple nodes. The login node is essentially a node with low memory dedicated to user interaction (e.g., writing scripts). The compute nodes have more memory and are dedicated to computation. A node is made up of multiple processors.

The ```-o``` option specifies the name of the standard output file (stdout). In this example, we use the job number (```%j```) and the first node (```%N```) as components of the filename (this is a good practice, but is not necessary). Anytime you use a command such as ```echo``` that typically prints something to your terminal screen, it will be appended to this file.

The ```-e``` option specifies the name of the error file. In this example, we use the job number (```%j```) and the first node (```%N```) as components of the filename (this is a good practice, but is not necessary). If your script runs into errors, the error messages will be printed to this file.

The ```--ntasks``` (```-n```) option specifies the number of parallelized tasks you will utilize.

When running a command in parallel, the SLURM built in ```$SLURM_NTASKS``` variable can then be used to denote the number of MPI tasks to run.

In case of a plain MPI job, this number should equal number of nodes (```$SLURM_NODES```) times number of cores per node.


# <a name="using-an-interactive-node"></a>
# Using an interactive node

Sometimes submitting a batch script is not efficient (you submit, then wait, sometimes wait some more, get distracted, and then check your output and see that you forgot a semi-colon). It is helpful to verify things interactively before submitting a full job, but you can't do this on the login node. Instead, you will work interactively on a compute node (similar to how you are accustomed to working in your local environment).

To open an interactive node, you will simply run an interactive job using the following command:

```
salloc --time=1:00:00 --ntasks 1 --nodes=1
```

This provides you with a computing space with a one-hour time limit where you can run commands/scripts you would run on a compute node using a batch submission. Because clusters can be very busy, some space has been designated to interactive jobs on the notchpeak cluster (up to 8 hours of wall time can be used with a maximum total of 32 tasks and 128 GB memory). To access one of these interactive nodes, you can run the following command **after logging on to the notchpeak cluster** (This isn't necessary for this exercise, but may be of use down the road):

```
salloc -N 1 -n 2 -t 2:00:00 -A notchpeak-shared-short -p notchpeak-shared-short
```
















