# Computing

@[toc]

## Getting Help

### Robarts IT Support

For general IT issues (cannot log into your Robarts account, problem with internet connectivity, printing etc) you can contact the Robarts IT helpdesk by sending an e-mail to helpdesk@robarts.ca.  Their website also has information for printing and other common software. 
http://itcentre.robarts.ca/

### Lab IT support
For lab-specific issues, you can contact Yingli (@yinglilu) on Khanlab slack


## Data and Filesystems

### Best practices

Project data, code, and any files you are working on or creating, should be kept on a network filesystem, since these are regularly backed-up. 

Best practice is to use:
* Github (public/private) for code, scripts, markup docs
* /scratch filesystem for active projects (<60 days old) 
* /project filesystem on Graham for long-term imaging data, where smaller files are tarred up
* Dropbox/OneDrive/Google for any other data, manuscripts etc
* Your local machine or /scratch for disposable scratch data

#### Deprecated - Robarts Imaging  Filesystems (eq-nas)

Robarts Imaging has a Windows filesystem which we have used to store some of our data. This system is going to be phased out in the near future, so new study data should utilize Compute Canada instead. It can be accessed from Windows, Mac or Linux, but performance is not very good from Linux, especially when dealing with many files.

The relevant filesystems, mounted on the imagingsrv.robarts.ca filesystem are:
`EpilepsyDatabase`, `EpilepsyHistology`,`khan_Data$`

These migrated from eq-nas to imagingsrv in early August 2019. These are being phased out, so no new data should be placed on these drives. 


* On Windows, You can access these in windows with the URL in explorer:
`\\imagingsrv.robarts.ca\EpilepsyDatabase`

* On Mac, you can use the URL:
smb://imagingsrv.robarts.ca/EpilepsyDatabase
(enter this in Finder -> Go -> Connect To Server)

* On Linux, these shares are configured when your machine/user is configured, and should be available in ~/EpilepsyDatabase if you use:
mount ~/EpilepsyDatabase

Users without root permission:
* If the link ~/EpilepsyDatabase does not work, your user on that machine has not been configured, contact an admin (currently Ali)

Users with root access:
* Make sure you have git and cifs-utils and then use
```git clone http://github.com/akhanf/vasst-dev.git; sudo ./vasst-dev/sys_utils/setupNewUser $USER ``` where $USER is your Robarts username 
* Then, `ln -s /eq-nas/USERNAME/khangrp ~/khangrp`
* Then, `mount ~/khangrp` and finally, enter your Robarts password



## Installing Software

Graduate students will have access to install lab-related software on their workstation. Other students will need to request software installation from an administrator (see @alik or @yinglilu)

### MATLAB

Western has a campus-wide MATLAB license. You can sign up for it here 
http://www.uwo.ca/its/sitelicense/matlab/

### ROAMS (Remote desktop access)

If you would like remote access to lab computing resources, please apply for a ROAMS account here:
https://remote.uwo.ca/

Instructions
1. Log in
2. Request->Make request
3. Click start for roams.
4.  Department is : RRI
5. Group is : General roams use – Imaging.

Once your ROAMS account is approved, you can access your workstation the instructions provided by ITS.  This can be used to access your machine via SSH, Windows RDP or RealVNC.

## Working in Linux

Most software for neuroimaging is written for Linux. The preferred way to use Linux is through the CBS Server Virtual Desktop Infrastructure. 

Alternatives are either 1) install Linux on your workstation, 2) use a virtual Linux OS on your Windows/Mac workstation.

### Preferred: Use the CBS Server

More info here:
https://osf.io/k89fh/wiki/Computational%20Core%20Server/

All Khanlab users will automatically be granted access to a standard (light - 4core) account. Please contact me if you need a heavy account (>16core) account.

### 1. Installing Linux on your workstation

See Yingli and Ali to discuss a dual-boot install of Ubuntu LTS on your workstation (only recommended for Linux experts, and not recommended in general).

### 2. Virtual Linux OS

Instructions for using Vagrant and Virtualbox are here:
https://git.cfmm.robarts.ca/cfmm/singularityvagrant


##### Setting up the Ditto (CFMM area) printer in Linux

* Open up the search and search for “printers”
* Click “Add” in the top left area
* Click the “Network Printer” tab to expand it
* Scroll down and select “LPD/LPR Host or Printer”
* Under “Host:” type in “zeus.robarts.ca/Imaging-Ditto.imaging.robarts.ca” and click “Forward”
* Stay in “select printer from database” and scroll down to select “Toshiba” and click “Forward”
* Scroll down and select “e-Studio 4500c” and click “Forward”
* Type in a name and description for the printer and click apply
* Print a test page if you’d like

## Windows Subsystem for Linux 2 (WSL 2)

Follow the instructions here to set up a Linux environment on Windows! 

https://github.com/greydongilmore/operating_system_installs_mods/blob/master/wsl_docs/WSL2.md
