# Getting started with Compute Canada

This document is designed to help new lab members get started with Compute Canada, and also as a reference to existing members. This is a living document, please feel free to add to this, update it, or correct it. Also feel free to add any suggestions or questions you have, there is an empty FAQ section at the bottom of this as well!

Join the #sharcnet slack channel for up to date info

@[toc]

## Getting an account

These tools allow you to access servers supported by the Canadian government for research purposes, which are useful for jobs that are very computationally heavy.

Create an account at: 

https://ccdb.computecanada.ca/security/login

Make sure to include your Sponsor, Ali Khan (ask him for access). Once Ali accepts your sponsorship request, you can apply for a consortium (the specific server or cluster which you intend to use, in my case, SHARCNET) at:

https://ccdb.computecanada.ca/me/facilities

Allow some time for that application to be accepted as well, and for SHARCNET you’ll want to complete the ‘New User’ webinar (register after your application to the consortium has already been accepted). This webinar will require you to use Vidyo (https://freetrial.vidyo.com/web-free-trial/), for which you should use your SHARCNET login information as well. Once you have an account, use the Vidyo link you get upon registration to connect. After your Webinar, SHARCNET will send you a link to an online quiz which will give you privileges to actually use SHARCNET.

NOTE: you no longer have to apply for a SHARCNET consortium to access graham. Once Ali accepts your sponsorship request you can log in to system with

ssh <username>@graham.computecanada.ca

## Logging in and accessing your files

Login to the system with
```
ssh <username>@graham.sharcnet.ca   
```

### Passwordless SSH

If you are using sshfs, you should always have passwordless ssh set-up, else you may encounter issues with your IP becoming blacklisted due to too many failed reconnection attempts. 


Follow instructions here: (this worked for me connecting from my Mac to sharcnet)
https://help.dreamhost.com/hc/en-us/articles/216499537-How-to-configure-passwordless-login-in-Mac-OS-X-and-Linux

Instructions repeated here:
1. Generate a ssh keyfile (hit enter when asked for passphrase)
```
ssh-keygen -t rsa
```
2. Copy this to the remote server
```
ssh-copy-id -i ~/.ssh/id_rsa.pub USERNAME@graham.sharcnet.ca
```

### Mounting graham data locally with sshfs

See install instructions here, however, make sure to use the options highlighted below:
https://www.sharcnet.ca/help/index.php/SSHFS


```
mkdir ~/graham;
sshfs -o reconnect,ServerAliveInterval=15,ServerAliveCountMax=3,follow_symlinks $USER@gra-dtn1.computecanada.ca:/home/$USER ~/graham;
```

Note: if you are experiencing permission denied issues when accessing some of your files through sshfs, you may need to manually override the uid and gid settings. See below:
1. On your local system, run `id` at the command-line to get the numeric id for your `uid` and `gid`. Make note of these, will be referred to below as NUID, NGID
2. add the following option to your sshfs command:
```
-o uid=NUID,gid=NGID
```

#### Windows SSHFS
Full details can be found here: https://github.com/greydongilmore/operating_system_installs_mods/blob/master/windows_docs/windows_sshfs.md
1. Install the latest stable installer of [WinFSP](https://github.com/billziss-gh/winfsp/releases)
2. Install the latest stable installer of [SSHFS-Win](https://github.com/billziss-gh/sshfs-win/releases)
3. Open **File Explorer**, right-click on **This PC** and choose **Map network drive**. Choose a drive to mount at and enter the following in the folder field: `\\sshfs\<USER>@graham.computecanada.ca` 
4. Ensure you check the box **Connect using different credientials if your computer login password is different from Graham.
5. The network drive should then appear in your file explorer!

#### Windows alternative (sftpnetdrive)

1. Google sftpnetdrive
2. download the free personal-use version (you only need to provide name and email to download)
3. install it with default settings
4. login using sharcnet address (e.g. gra-dtn1.computecanada.ca), your sharcnet account and password (or key), and choose a drive name (e.g. I for internet)
5. enjoy

### Using globus to transfer files

Log on to globus: https://globus.computecanada.ca
Click "Endpoints" tab and subsequently subtab "administered by me"
If no personal endpoints, add "Globus Connect Personal endpoint"
	Set a Display Name and download the appropriate application for your OS
Once successful, go to "Transfer Files" tab and set Endpoints depending on direction of transfer
For transfer to/from graham: use computecanada#graham-dtn


### Setting group read write permission

Run these commands the first time you login to a new cluster system to grant *group* read/write to project folders (to allow easier debugging):
```
chgrp -R rrg-akhanf ~/projects/*/$USER; 
chmod -R g+rwXs ~/projects/*/$USER;
```

To grant access to your home directory:
```
chgrp -R rrg-akhanf ~; 
chmod -R g+rwXs ~;
```

## Running jobs

### Neuroglia-helpers

The neuroglia-helpers scripts provide an easier interface to submit jobs, including a bids-app job submission script (bidsBatch). 

To install the neuroglia-helpers scripts - see README here:

https://github.com/khanlab/neuroglia-helpers/blob/master/README.md


### Submitting and monitoring jobs directly

Hello, World! on graham

Follow the instructions here: https://docs.computecanada.ca/wiki/Running_jobs

#### Useful SLURM Commands
```
squeue -u <username> # see what's on the queue (running/waiting)
sshare -U <username> # check share usage
sshare | grep akhanf # check share usage for entire lab
scancel <jobid> # remove job
scancel -u <username> # remove all your jobs -- BE CAREFUL.
sacct -j <jobid> --format JobID,ReqMem,MaxRSS,Timelimit,Elapsed   # check completed job usage
```

Run the following to set-up your bashrc to enable more readable sacct output:
```
echo “export SLURM_TIME_FORMAT=relative” >> ~/.bashrc
echo “export SACCT_FORMAT=JobID%-20,Start%-10,Elapsed%-10,State,AllocCPUS%8,MaxRSS,NodeList” >> ~/.bashrc
```


#### SBATCH instructions/parameters

https://slurm.schedmd.com/sbatch.html

Important to understand compute node specs and appropriately allocate resources using some combination of parameters like --mem, --cpus-per-task, --ntasks, etc.

Node types and characteristics here: https://docs.computecanada.ca/wiki/Graham

The neuroglia-helpers job templates are optimized for the size of nodes and our allocation on graham:

e.g.:
* Skinny: 1C/4GB/24hr  (1/32 of a node)
* Regular: 8C/32GB/24hr   (1/4 of a node)
* Fat: 32C/128GB/24hr  (whole node)

* Short: 8C/32GB/3hr (Regular, for 3hrs)
* Long: 8C/32GB/72hr (Regular, for 3 days)
* Quick: 1C/4GB/0:10hr (Skinny, for 10 minutes)

## Visualization 

### Using Graham's virtual desktop to visualize your data
 
1. Install TigerVNC on your client (https://github.com/TigerVNC/tigervnc/releases)
2. Connect to: gra-vdi.computecanada.ca
3. If you have neuroglia-helpers installed, you should be able to use fsleyes and itksnap

 Note: This is not a compute node and should only be used for viewing images and light processing. If you want to use VNC for heavier processing, you can request an interactive node and connect via an ssh tunnel (see https://docs.computecanada.ca/wiki/VNC)

## Software 

### Python
Python2 is installed on graham for everyone to use, and you can install packages to your home directory by using the --user flag when you run pip install or python setup.py install. 

1. Create the local paths for packages:
```
mkdir -p  ~/.local/lib/python ~/.local/bin
```
2. Set-up the paths in your ~/.bashrc file. Add these lines to your .bashrc file (and re-login afterwards):
```
PYTHONPATH=$PYTHONPATH:~/.local/lib/python
PATH=$PATH:~/.local/bin
```
3. Then, to install packages with pip you can use:
```
pip install --user <packages>
```
Or to install a package that has a setup.py file, use:
```
python setup.py install --user 
```
#### Creating and using python virtual environments 
1. Select python version of your choosing:
```
module load python/<version>
```
2. Create virtual environment with ( note that ENV is the name of the directory containing your environment, you can name this whatever you want):
```
virtualenv --no-download ~/ENV
```
3. Active the virtual environment.
```
source ~/ENV/bin/activate
```
4. Upgrade pip in the environment.
```
pip install --no-index --upgrade pip
```
5. To exit the virtual environment enter the command deactivate:
```
deactivate
```
more information can be found at: https://docs.computecanada.ca/wiki/Python

### MATLAB (updated Jan 2018):

Request access to Western TAH license on sharcnet by sending an e-mail to bge@sharcnet.ca with your WesternID, sharcnetID, and position (student, post-doc etc).


Run the following to set-up your environment: 
```
module load matlab
```
It is also setup in the neuroglia-core singularity image (shub://khanlab/neuroglia-core)

For your own singularity images you can add the following to your environment:
```
#matlab on graham (requires user to be on sharcnet matlab users list)
export JAVA_HOME=/cvmfs/soft.computecanada.ca/easybuild/software/2017/Core/java/1.8.0_121
export MLM_LICENSE_FILE=/cvmfs/restricted.computecanada.ca/config/licenses/matlab/inst_uwo/graham.lic
export PATH=/cvmfs/restricted.computecanada.ca/easybuild/software/2017/Core/matlab/2017a:/cvmfs/restricted.computecanada.ca/easybuild/software/2017/Core/matlab/2017a/bin:/cvmfs/soft.computecanada.ca/easybuild/software/2017/Core/java/1.8.0_121:/cvmfs/soft.computecanada.ca/easybuild/software/2017/Core/java/1.8.0_121/bin:$PATH
export _JAVA_OPTIONS=-Xmx256m
export LIBRARY_PATH=/cvmfs/soft.computecanada.ca/easybuild/software/2017/Core/java/1.8.0_121/lib
```

### Datalad

Datalad (http://datalad.org) is a tool for downloading, working with, and searching through online datasets. For a list of available datasets see http://datasets.datalad.org  

Datalad datasets are git repositories that you can download, where the large files are symlink placeholders until you use the `datalad get` command on them to download the full file. 

#### Getting started with datalad:

##### Install datalad python package:
```
pip install --user --upgrade datalad
```
Note: there is a singularity container for datalad available, but it had network issues when I tried it.. (AK)

##### Install git-annex:
See instructions here: http://git-annex.branchable.com/install/

If on graham, the linux standalone tarfile is shared, so just need to add this to your .bashrc:
`PATH=/project/6007967/software/git-annex.linux:$PATH`

##### Try it out, get a dataset:

This command will get the an openneuro datalad dataset (won't download the large files yet):
```
datalad install ///openfmri/ds000001
```

You will see all the files there, but the large ones will be symlinks if you `ls -l`. To get the files, use `datalad get <filename/folder>`:
```
datalad get ds000001
```

or to get the files when you install, can use:
dataset (won't download the large files yet):
```
datalad install -g  ///openfmri/ds000001
```

### Jupyter Notebooks on Graham

This documentation is for setting up an environment for Jupyter notebooks. It should be noted certain packages have dropped Python 2 support and may require installation of prior module versions. Portions of this documentation has been adapted from Compute Canada documentation (https://docs.computecanada.ca/wiki/Jupyter). This documentation provides the basics to set up a Jupyter environment. For more detailed installation, visit the Compute Canada documentation page.

#### Installing Jupyter Lab (creating a virtual environment)

1. Load the appropriate Python module (Python 2 or Python 3) and set up a virtual environment (see previous section). Install Jupyter & Jupyter lab to your virtual environment

```BASH
source ~/ENV/bin/activate
    
pip install jupyter
pip install jupyterlab
``` 

2. Submit the job

    `regularInteractive` supports job submission for Jupyter sessions. Before submitting your job, ensure your virtual environment is activated. Submit your job using the optional flag corresponding to Jupyter! 
    
```BASH
regularInteractive -j 
```
    
#### Connecting to Jupyter Notebook
**From Linux / MacOS**

Compute Canada recommends using the `sshuttle` Python package to create a tunnel between your compute and the compute cluster. 

On your computer, open a new terminal window and run the following to create the tunnel

```BASH
sshuttle --dns -Nr <user>@graham.computecanada.ca
```
    
Then copy and paste the provided URL into your browser. An example URL may look like the following: 

```html
http://cdr544.int.cedar.computecanada.ca:8888/?token=7ed7059fad64446f837567e3
```
    
**From Windows**

A tunnel can be created from Windows via MobaXTerm. This method will also work from any Unix-based system.

After submitting your Jupyter job, a URL similar to the one on the screen

```BASH
http://cdr544.int.cedar.computecanada.ca:8888/?token=7ed7059fad64446f837567e3
       └──────────────────┬─────────────────┘        └───────────┬──────────┘
                      hostname:port                          token
```

Open a second Terminal tab in MobaXTerm (Session 2). Substitute with the appropriate hostname:port of the URL

```BASH
ssh -L 8888:<hostname:port> <user>@graham.computecanada.ca
```

Open your browser and go to the following URL, replacing `<token>` with the token value

```html
http://localhost:8888/?token=<token>
```

## Experimental: SOSCIP GPU access

More info here:
https://docs.scinet.utoronto.ca/index.php/Mist


# Project space migration - May 2020

With migration from our previous project allocation (rrg-akhanf) to the new larger CFI allocation (contributed system, ctb-akhanf), it is up to us to migrate the project files we want to keep. It is important to make sure migrated folders do not have a large number of small files, since the total number of files is limited.

With the new space being 600TB and 30M maximum files, a reasonable rule of thumb is that folders with >500 files where the average file size is <20mb should be stored inside a tar file instead. 

We have some new tools to assist with migration from the  `rrg-akhanf` project space to `ctb-akhanf`. These are located in neuroglia-helpers.

Here are step-by-step instructions for performing the migration on your project folder. If you are unsure about any step (especially steps 2-4), STOP and ask for help to avoid any potential loss of data! 

## Step 1: Get usage stats

First step is to determine which folders can be migrated as-is, and which sub-folders require cleaning up.

The `listusage` tool determine the numbers of files and average file size in your folders. This tool prints to the screen (and to a csv file in `/scratch/$USER/listusage/...`), containing these statistics. 

Run the tool (e.g. on your project folder) using:
```
listusage /project/rrg-akhanf/$USER
```
This command may take a few minutes for larger folders -- can run it without any arguments to see additional options.

Feel free to share your listusage output with me (Ali), especially if you are unsure about the next steps, or identifying which folders need to be cleaned. 

## Step 2: Remove any unneeded files

The `listusage` output may reveal certain directories that have a high number of files that are no longer needed -- (e.g. outdated analyses, old job directories, etc..). Remove anything you do not need anymore. Do not remove folders with large number of files that you DO need to keep, these can be replaced with TAR archives in a subsequent step. 

## Step 3: Move and symlink folders

The `moveandlink` tool migrates folders, and leaves behind a symbolic link on the source project folder to ensure previous paths still function correctly (as long as the rrg-akhanf folder exists).

Use the dry-run option when you first run the tool to ensure the appropriate actions are being taken.

```
moveandlink -n /project/rrg-akhanf/$USER/some_project_folder
```

By default, moveandlink will not migrate folders with av file size <20mb, but you can use the `-f` option to force the move. Make sure you proceed with the next step in this case. 

Note: The move operation will be nearly instantaneous, since the files are not physically being moved around (since ctb and rrg folders are still on the same filesystem), but changing the group permission may take some time (depending on number of files). You should see plenty of output on the screen while it is doing this (`changed group of ...`)

## Step 4: Tar folders with small files 

The `archivefolders` tool will automatically tar-up folders that are below the avg number of files cutoff, submitting batch jobs for each `tar` job. 

Similar to `moveandlink`, make sure you run with the dry-run option `-n` to evaluate the actions:

```
archivefolders -n /project/rrg-akhanf/$USER/some_project_folder
```

NOTE: this tool will not remove the folder, but instead rename (mv) it to *.ARCHIVED. This is to prevent potential data loss and to also give you an opportunity to easily revert. Once you are satisfied with the tarfile replacement, please remove the .ARCHIVED folders yourself.

## Making use of archived data in the future

The best way to make use of data that has a large number of small files that exist in a tar file on project space, is to extract that folder on scratch, and work with it there.

e.g. to extract to scratch:
```
mkdir /scratch/$USER/some_project_folder
tar -C /scratch/$USER/some_project_folder/ -xvf /project/ctb-akhanf/$USER/some_project_folder/name_of_tar_file.tar
```


 # FAQ
 
 * Q1. I am getting a Disk Quota Exceeded error on /project space!
 
 * A1: Files/folders on the /project space must have the group permission rrg-akhanf to make use of our allocation. Please see the section [above](#Setting_group_read_write_permission_62) to reset your group permissions on your files/foldlab
 
 * Q2.  Singularity images that use python running on graham use python dependencies from the .local directory in my home directory rather than using python dependencies from the image.
 
 * A2 Python will automatically take dependencies from the .local directory and in order to work around this quirk we can create a new directory called fake_home with symlinks within it that call directories in our home directory (home/$USER) that we would actually like to use. In neuroglia-helpers/cfg/graham.cfg under singularity options add the flag --home followed by the path to the fake_home directory.
 
    1. Create fake home directory with sym_links.
         ```BASH
         cd ~
         mkdir fake_home
         cd fake_home
         
         ln -s <required directory in home/$USER>     #repeat this for as many directories as you require
         ```
    2. Open graham.cfg file and add --home flag to singularity opts with path to fake_home directory. Text after --home represents flag and path to be added to existing script
        ```BASH 
        nano ~/neuroglia-helpers/cfg/graham.cfg
         
        #Singularity options
        SINGULARITY_OPTS="-e -B /cvmfs:/cvmfs -B /project:/project -B /scratch:/scratch -B /localscratch:/localscratch --home /home/$USER/fake_home"
        ```

 
