<!-- TOC -->

- [High-throughput sequencing project guidelines](#high-throughput-sequencing-project-guidelines)
  - [Submitting a project on Gnomex](#submitting-a-project-on-gnomex)
  - [Raw sequence file backups](#raw-sequence-file-backups)
  - [MINIMUM metadata required for BioSample registration and NCBI SRA submissions](#minimum-metadata-required-for-biosample-registration-and-ncbi-sra-submissions)
- [CHPC Usage](#chpc-usage)
  - [General](#general)
  - [Starting an interactive shell session on Round Lab node](#starting-an-interactive-shell-session-on-round-lab-node)
  - [Starting interactive session on other partitions.](#starting-interactive-session-on-other-partitions)
  - [Mounting CHPC drives in Mac OS](#mounting-chpc-drives-in-mac-os)
    - [Your Home directory](#your-home-directory)
    - [Round-group shared drives](#round-group-shared-drives)
  - [Downloading raw sequence files from Gnomex to CHPC](#downloading-raw-sequence-files-from-gnomex-to-chpc)
  - [Using containers with Singularity](#using-containers-with-singularity)
  - [Display largest files in your home directory](#display-largest-files-in-your-home-directory)
- [Cheatsheets And Helpful Links](#cheatsheets-and-helpful-links)
- [Reproducibility Notes / Best Practices](#reproducibility-notes--best-practices)
  - [Data Management / Organization](#data-management--organization)
  - [Code documentation](#code-documentation)
- [Git usage](#git-usage)
  - [Getting started with Git: Setup a new project repository and push EXISTING project to GitHub from the command-line.](#getting-started-with-git-setup-a-new-project-repository-and-push-existing-project-to-github-from-the-command-line)
  - [Getting started with Git: From an existing, non-empty GitHub repository](#getting-started-with-git-from-an-existing-non-empty-github-repository)
  - [.gitignore template](#gitignore-template)
- [Using Google Backup and Sync to sync your local files to Google Drive storage](#using-google-backup-and-sync-to-sync-your-local-files-to-google-drive-storage)
- [Statistics](#statistics)

<!-- /TOC -->

# High-throughput sequencing project guidelines
## Submitting a project on Gnomex
- Have your metadata (Animal ID, Genotype, housing, conditions, etc.) for your experiment ready before you submit on Gnomex.
  - We want metadata for the project documented at time of submission. Gnomex experimental details will remain on Gnomex and tie the sequence library prep method, sequences and metadata together.
  - See the minimum metadata requirements section below. These are **minimum to publish your data!** We should always exceed these requirements within our lab (depending on the sample type)
  - Prepare in spreadsheet program with each variable in its own column, and export as tab-separated value file. Then, upload to Gnomex during submission. Gnomex will allow you to match column headers as desired.
  - Having the metadata at submission allows you to automatcialy tie metadata to individual sequenced libraries after project is done. Go to "Experimental Design" and click "Download Sample Sheet" button after sequencing is done. This now lists each sample you submitted by its sequence library ID (for example, 13567X10) which is the preferable naming scheme to maintain.
  
## Raw sequence file backups
- Gnomex will remove your sequence files after a few months. It is NOT a long-term storage.
- Two backup locations for raw sequence files are required. Sumbitter/Experimenter is required to check they are both present.
  1. Seven-bridges. Files will be automatically transferred here from Gnomex after 6 months. User should make sure they are "archived" on 7-bridges though to reduce storage costs. If metadata was properly filled at submission, this is sufficient beacause it will be transferred along with files. If not, user needs to add a metadata file to 7-bridges as well.
  2. Round lab Path-server. Directory = "illumina_lanes_archive". User must tranfer them here themselves. Include the metadata file in your directory. Only transfer compressed (usually gzip) files. 

## MINIMUM metadata required for BioSample registration and NCBI SRA submissions

Sequences used in any publication are required to be deposited in a publicly available archive. NCBI's Sequence Read Archive (SRA) is the most common source, and generally it is required to deposit our publicly-funded research here, and certainly preferred. In order to do this, deposited sequences are connected to a "BioSample" which must be registered with some minimum metadata depending on the sample type. It is therefore critical that this metadata is recorded for all sequenced samples. Notably, the requirements are extremely minimal and we really want to surpass these to make publicly available data as useful to the scientific community as possible. The best idea is to follow the Genomic Standards Consortium guidelines and use their template spreadsheets ([https://gensc.org/mixs/](https://gensc.org/mixs/)).

These requirements are continually changing, but as of February 2021: 

**Model organisms**
1. One of: Strain, isolate, breed, cultivar, ecotype
2. One of: Age, Developmental Stage
3. Sex
4. Tissue

*Note*: These are minimum from NCBI and the Genomic Standards Consortium has proposed minimum standards slightly more than this, and also a controlled vocabulary that makes deposited more useful to everyone. Also, they provide spreadsheets to help you fill in the information. It is highly recommended to use these standards [https://gensc.org/mixs/](https://gensc.org/mixs/) and will actually make it easier to upload to NCBI in the end.

**Environmental, 16S, metagenomes, other**
See the genomics standards consortium packages: [https://gensc.org/mixs/](https://gensc.org/mixs/)

# CHPC Usage
## General
- Our whole group is limited to 2 TB total. Across all user's home spaces! This removes the 50 GB limit for each user, so one user could potentially take up all space. Be aware of your space usage and check every so often by logging into CHPC's main page and going to your profile if you are not sure.
- Home space is backed up (as of July 2020) with snapshots. However, **round-lab1 and round-lab2 are not backed up.**
- Use scratch space for big temporary files such as raw fastq and intermediate files. Generally, better to write your scripts to have output to scratch and copy over only the important outputs to your home directory. Scratch locations are often changing so see search "storage" on CHPC's home for these locations.
- Be considerate, try not to take more computational resources than you need for interactive sessions and don't leave long running sessions if you aren't actively using them.

## Starting an interactive shell session on Round Lab node
1. Log on to notchpeak cluster with your method of choice. **One** of:
   1. Using "Terminal" on Mac: `ssh Your_uNID@notchpeak.chpc.utah.edu`
   2. Using a Terminal from OnDemand Portal from CHPC: [https://ondemand.chpc.utah.edu](https://ondemand.chpc.utah.edu). Go to "Clusters" -> ">_Notchpeak Shell Access"
   3. Using Fastx Web client: In browser navigate to: [https://notchpeak1.chpc.utah.edu:3443](https://notchpeak1.chpc.utah.edu:3443). Click "Launch Session" and choose any terminal or virtual desktop you prefer.
2. Use 'srun' command to obtain an interactive session with bash as your shell. Change values for time and `-n` (number of processors; up to 32 are available) as desired. **DO NOT request more time or processors than you need**, but generally ``-n 2` minimum. Time is in HOURS:MINUTES:SECONDS format.
```bash
srun -A round-np -p round-shared-np --time 4:00:00 -n 2 --pty /bin/bash -l
```

If you need the entire round node, you can change the partition value to `-p round`. This should be avoided, so others can use it, and instead use the round general allocation on other clusters as described below.

## Starting interactive session on other partitions.
We also have an allocation that allows us to use time on other clusters and partitions. See [CHPC's documentation for more info on parititions, nodes and clusters](https://www.chpc.utah.edu/documentation/guides/index.php). Here is an example of how to get an interactive session on notchpeak-shared partition, using the round account allocation.

1. Log on to cluster of your choice (here shown for notchpeak) with your method of choice. Similar method can be used for other clusters, just replacing "notchpeak" with other cluster name (for example, kingspeak). Use **One** of:
   1. Using "Terminal" on Mac: `ssh Your_uNID@notchpeak.chpc.utah.edu`
   2. Using a Terminal from OnDemand Portal from CHPC: [https://ondemand.chpc.utah.edu](https://ondemand.chpc.utah.edu). Go to "Clusters" -> ">_Notchpeak Shell Access"
   3. Using Fastx Web client: In browser navigate to: [https://notchpeak1.chpc.utah.edu:3443](https://notchpeak1.chpc.utah.edu:3443). Click "Launch Session" and choose any terminal or virtual desktop you prefer.
2. Use 'srun' command to obtain an interactive session, changing time and proceses (`-n`) as desired:
```bash
srun -A round -p notchpeak-shared --time 4:00:00 -n 2 --pty /bin/bash -l
```

## Mounting CHPC drives in Mac OS
- Updated March 2021 for new samba mounting group space on CHPC
- Note that mounting locations can change, so these paths may need to be updated in the future.
- See CHPC guide for additional details: [https://www.chpc.utah.edu/documentation/data_services.php#Direct_mounts](https://www.chpc.utah.edu/documentation/data_services.php#Direct_mounts), including method for mounting on Windows.

### Your Home directory

1. On you Mac connect as you do for Round Lab server:
   1. Go -> Connect to server...
2. Enter server name with your (replacing uNID with your actual uNID): `smb://ad;uNID@samba.chpc.utah.edu/round-home/uNID`

### Round-group shared drives
1. On your mac connect as you do for Round Lab server:
   1. Go -> Connect to server...
2. Enter server name, as below, replacing your uNID:
   1. round-group3 (the drive formerly known as round-group1): `smb://ad;uNID@samba.chpc.utah.edu/round-group2`
   2. round-group2 (General shared space): `smb://ad;uNID@samba.chpc.utah.edu/round-group3`
   3. round-group4 (Reference seq datasets): `smb://ad;uNID@samba.chpc.utah.edu/round-group4`

## Downloading raw sequence files from Gnomex to CHPC
For near-term storage raw sequences can be kept on round-group3 drive in:
`/uufs/chpc.utah.edu/common/home/round-group3/raw_illumina_seq`. This space (not backed up) is useful to store seqs you are actively working with and should not be used as long term storage. 

Space can become limited here often (especially with bigger NovaSeq files), so you may need to download directly to scratch space instead.

Regardless of destination location, in order to donwload from Gnomex to CHPC:
1. Click on your Experiment in Gnomex, then the "Files" tab. 
2. Click "Download Files" button and a separate window pops up.
3. Drag the files you want to download (the fastq files minimally) to the right. 
  - Be patient, this can be a bit slow to populate if you have a lot of files.
  - Recently there is a "manifest" file here which relates each lengthy file name in a column to the sample ID. This can be a handy starting template for many other analysis programs that require some type of similar manifest file for input.
4. Click on the "FDT Command Line" button. This pops up a separate window with a shell command and some instructions. 
5. Ignore the instructions and just copy the full command (not the one under "For a Windows system..").
6. In your script or a shell (depending on if job submitted batch script or interactive) move to the location you want to download your files.
7. Paste the copied command, then replace "./fdtCommandLine.jar" with a reference to the fdt.jar file on our shared space, located here: `/uufs/chpc.utah.edu/common/home/round-group1/bin_shared/fdt.jar`. 
  - Note that uou can also just redownload the .jar file as instructed by Gnomex and refer to that new download.
  - You can also point to a separate place to download your files by changing the path after the `-d` option. It is set to your current directory `./` by default.
8. After entering the command, your files should be rapidly transferring to the location you specified. Make sure to include a metadata file there.

  a. In Gnomex experiment still, click on "Experiment Design" tab.
  b. Click "Download Sample Sheet", and upload this file to your gnomex metadata and/or raw files location.
  c. This file can be your sample mapping file or metadata file as-is (if you entered all metadata at time of submission), but at the least serves as an excellent starting template for a metadata file.
  
9. After your files have downloaded, makes sure to set permissions so round-group users can read and write these (you can change as desired, but best to keep write permissions so that they can be managed/deleted if needed to free up space). Replace DIRECTORY_NAME with the directory within `raw_illumina_seq` where your files are:
```bash
chmod -R 775 DIRECTORY_NAME
```

## Using containers with Singularity
Containers are cached after the first time you call them. They can take up a large amount of space and each user doesn't necessarily need to have a same copy of the same container. So, set your cache to a shared, writeable location on round-group1.
1. Use a text editor to open a file in your home diretory called .bash_profile, which likely already exist. For example, with nano:
```bash
nano ~/.bash_profile
```
2. Add the following line to set the variable SINGULARITY_CACHEDIR to the shared directory on round-group1
```bash
export SINGULARITY_CACHEDIR=/uufs/chpc.utah.edu/common/home/round-group1/containers/SingularityCache
```
3. Save the .bash_profile file (use Ctrl+X with nano), then source it (or just log out and log back in again)
```bash
source ~/.bash_profile
```

## Display largest files in your home directory
We only have 2 TB shared amongst all users' home spaces so it is important to try to avoid maintaining many large files in home space. To determine your top 100 largest files (recursively from the current directory `./`) in human readable format:
```bash
cd ~
du -hx ./ | sort -hr | head -n 100
```
This command can take some time to run depending on how many files you have in your home space.

# Cheatsheets And Helpful Links
**bash**:


- [Level 1: common bash commands](https://scilifelab.github.io/courses/ngsintro/1902/files/Bash_cheat_sheet_level1.pdf)

- [Level 2: regex and variables in bash](https://scilifelab.github.io/courses/ngsintro/1902/files/Bash_cheat_sheet_level2.pdf)

- [String split & variable substituion in filename examples in metagenomics](http://www.metagenomics.wiki/tools/ubuntu-linux/shell-loop/string-split)

**R**

All the R cheatsheets you could hope for on [RStudio page](https://rstudio.com/resources/cheatsheets/). Some specifics:

  - [Data transformation in dplyr (tidyverse)](https://github.com/rstudio/cheatsheets/blob/master/data-transformation.pdf)
  - [Regular expressions in R](https://github.com/rstudio/cheatsheets/raw/master/regex.pdf)
  - [Base R](http://github.com/rstudio/cheatsheets/raw/master/base-r.pdf)
  - [vegan: community ecology](https://github.com/rstudio/cheatsheets/raw/master/vegan.pdf)
  - [R markdown](https://github.com/rstudio/cheatsheets/raw/master/rmarkdown-2.0.pdf)
  - [ggplot2: graphing](https://github.com/rstudio/cheatsheets/raw/master/data-visualization-2.1.pdf)
  - [Factors in R with forcats (tidyverse](https://github.com/rstudio/cheatsheets/raw/master/factors.pdf)
  

# Reproducibility Notes / Best Practices
*"your most likely collaborator is yourself"*
Most importantly, remember that every little bit helps and you don't have to be perfect at first. Some people don't ever start this change because it feels too overwhelming. In the end, it will make your life easier as well. There is also a movement away from paper supplements and pushing that data to repositories. Making these changes will make publication easier for you to share details and methods more clearly.

Links:
- 2020 Reproduciblity for Everyone Workshop Video: [https://www.youtube.com/watch?v=44p0oENCJkg](https://www.youtube.com/watch?v=44p0oENCJkg)
- Reproducibility for Everyone: [https://www.repro4everyone.org/](https://www.repro4everyone.org/)
- Riffomonas (Schloss Lab) Reproducible Research Tutorials/Training: [http://www.riffomonas.org/reproducible_research/](http://www.riffomonas.org/reproducible_research/)

## Data Management / Organization
Think and organize 'project-centric'. Likely, you already organize by projects to some degree, but if all information related to a project is not in one subdirectory it will be lost or difficult to connect. Default OS directory structures tend to emphasize an organization by type of file and users often replicate this, leading to disconnected files. Instead, organize around your project. You still can organize downstream from their however you like. There's just a couple principles we must follow:
- Top-level project directory contains everything related to a project.
- Use readme files throughout to explain contents or outside sources.
  - Name these: README.md and write in plain text (with markdown syntax ideally).
  - Specify locations of large external files (FCS, fastq, etc) here.
  - Links (or aliases) can be included for ease, but don't rely on them for final project (servers locations change, files move, git doesn't normally follow links).
- Use consistent naming for experiments. A good convention is to start with date of  experiments in YEARMONTHDATE (or YEAR-MONTH-DATE) format.

For example:

```
Project_Name
  Raw-OR-Input_Data
    link_to_FCS_files_on_server
    README.md
  Methods-OR-eNotebooks
  Analyses
    FlowCytometry_Analysis
    16S_Analysis
    Metagenomics_Analysis
  Code-OR-Scripts-OR-Jobs
  Manuscripts
    Version_1
  README.md
```

## Code documentation
- Minimally, the code or scripts themselves (slurm or bash job scripts) should be provided within your project.
- Markdown or RMarkdown documents are preferred because you can comment naturally and format around your code.
  - RMarkdown or Jupyter Notebooks allow you to run your code from within your markdown documentation so are the best solution.
- Any code that processes raw data to tables for analysis should be considered required as part of your methods. Hosting these on GitHub is a great way to provide them.
- Ideally, we should also provide code that analyzes processed data and creates figures. This also would fit nicely in a GitHub or other publicly-faciing project repository.

# Git usage
Git is the program we use for version control. GitHub is the website repository we use for the Round Lab, but there are other repositories you can use as well (CHPC uses GitLab and you will have an account there with your uNID credentials already). In other words, you can use git with other repositories besides GitHub (like GitLabs) if you prefer.

- GitHub has a maximum file size of 100MB and max repository size of 100 GB. So, raw data files (fastq, fcs) should generally not be there, but smaller processed data (big .tsv/.csv file, .qza, .qzv) can usually fit.
- Git has a GUI you can install on Mac or PC if you like.
- CHPC's git documentation page provides more details: [https://www.chpc.utah.edu/documentation/software/git-scm.php](https://www.chpc.utah.edu/documentation/software/git-scm.php)
- Help with git commands: `git help CommandName`
- **A very good getting started with Git guide**: [http://rogerdudler.github.io/git-guide/](http://rogerdudler.github.io/git-guide/)

## Getting started with Git: Setup a new project repository and push EXISTING project to GitHub from the command-line.
It is a bit simpler to start with an empty GitHub repository and then initialize it in your project's directory as is shown here. If you already have files in your GitHub repository online (for example, if you started one with a README.md), you need to first "clone" that to your project directory to make sure they match up. Follow the next section instead ("Getting started with Git: From an existing, non-empty GitHub repository") if this is the case.

1. Sign up for user account on Github. [Only needs to be done once]
2. Create a new repository on Github website for your project. You can change the name later, but it's wise to consider this will ultimately be public facing and attached to a paper likely. Notably, the Git repository name does NOT have to be the same name as your project folder locally (on CHPC or local computer).
   1. Public is easier, but may want to keep private at first. If private, you will have to actively invite others to work with you.
   2. Add a short description.
   3. Usually, don't check the "initialize with a readme" button. For simplicity to start, it is better to create this README.md file locally and start with an empty repository on GitHub. Leave the "Add .gitignore" and "Add a license" buttons to "None" for now.
   4. Click "Create Repository"
   5. The next page has instructions similar to what I've outlined here. All you really need is the URL. Click the clipboard icon (or highlight and copy) to copy the URL (https://....) to your clipboard, or leave this page open while you follow the rest of instructions.
3. Load git on CHPC or install on your local machine if you like (https://git-scm.com/downloads). Generally, it is easier to just work remotely on CHPC for all your bioinformatics however you may want to pull repositories to your desktop/laptop as well (for example working in RStudio locally on a larger project). On CHPC:
```bash
module load git
```
4. Configure git [Only needs to be done once, if using `--global`]. The username will allow git to associate you with your files and is your actual name, not your GitHub user ID. It is good to add your email as well. You can set different names for different repositories/projects if you remove the --global option and perform this setup in each repository. We would prefer keeping the same name and email for all the lab's repositories.
```bash
git config --global user.name "Your Name (not GitHub ID)"
git config --global user.email "YourEmailHere"
```
5. Move to your project directory on CHPC or on your laptop/desktop. Create a new direcory (`mkdir`) if you don't already have one.
```bash
cd YOUR_PROJECT_DIRECTORY
```
6. (optional) Create a file to tell git which files to ignore (a .gitignore file)
- Here, using the built-in text editor nano, but you can use whichever editor you prefer including those on your desktop or CHPC built-in editor within OnDemand. The file just must be named exactly (without quotes) ".gitignore"
```bash
nano .gitignore
```
- Add to that file (one entry per line) any other file names you would like git to ignore or not track. Generally, you would want to use wildcards to ignore certain file types (for example, all .outerror files could be specified using `*.outerror`).
- Git will traverse all directories in your project directory looking for these files. You can specify if it should only look in the current directory or specific subdirectories or just about any other combo you can think of by using "/" and "*" together. [This page has good examples.](https://www.atlassian.com/git/tutorials/saving-changes/gitignore).
7. (optional at this stage) Create a readme file with markdown syntax that will display on your project's page. Use a text editor to create a file called README.md in your project's main directory. You can just type a brief paragraph without worrying about formattting, or use markdown syntax to do some basic formatting. You can always add this later, but should always have some readme file here. GitHub will interpret and display this file for you in any of your projects directories so long as it is called README.md.
8. Initialize your repository. Still, within your project directory:
```bash
git init
```
9. Link your project directory with your empty online GitHub repository:
```bash
git remote add origin YOUR_GITHUB_REPOSITORY_URL
```
Usually not needed, but to make sure you are on the "main" branch of your project. While branches are useful, you may never need to use them and stay on main. Note if comparing to online searches or help docs that this used to be referred to as "master" instead of "main".
```
git branch -M main
```

10.  Now, add the files you want git to track. You are actually all setup at this stage if your project directory is still empty (you didn't create a .gitignore or README.md file, or have other files already in your directory).
```bash
git add README.md .gitignore
```
Notice you can add multiple files at once separated by space, or use wildcards. Use tab autocomplete often. Git knows which are already added so will complete those that aren't.
11. Commit your files added. You must add a commit message everytime (to say what you did - it shoudl be short) or Git will not commit. After typiong this command a file will come up for you to add your message. Add it after the commented lines (#). Commonly, just add "initial commit" for this first commit.
```bash
git commit
```
After adding your message, close and save the file. If in nano: Ctrl+X
12. Push your changes to your GitHub repo
```bash
git push -u origin master
```
Will ask for your GitHub username and password, unless you setup a local key pair.

## Getting started with Git: From an existing, non-empty GitHub repository
If you prefer, you can setup your repository on GitHub with a README.md and/or .gitignore file at first. This means your repo won't empty, so you need to first make sure your GitHub repo and local project are in line.
1. Follow steps 1 - 4 above, but in step 1 you will have created a README.md and/or .gitignore file online. You can edit and preview these online.
2. Move to the directory one above where you want your project directory to be. In this scenario, you should not already have a local project directory and git will create it when you clone the existing repo in next step.
3. Clone the repository online to your local directory:
```bash
git clone YOUR_GITHUB_REPOSITORY_URL
```
4. You can now add, commit and push other files as above.

## .gitignore template
Here is a .gitignore template file: [https://github.com/RoundLab/Docs/ExampleTemplate.gitignore](https://github.com/RoundLab/Docs/ExampleTemplate.gitignore)

# Using Google Backup and Sync to sync your local files to Google Drive storage
With your uNID you have unlimited storage on Google Drive. This can be used to sync your local desktop files to the cloud (Google Drive) and access and edit them anywhere using GSuite (Google Docs, Sheets, etc.)

1. Install Google Backup and Sync
  - Notably, we are using this as a sync solution to facilitate remote work. Backup of data is managed by Path-IT. Thus, it likely makes sense to not sync your entire desktop and definitely don't rely on this as a backup solution.
  
2. Sign in with University of Utah Google account.
  - uNID@gcloud.utah.edu (works)
  - 2 step verification required.

3. (optional) If the app is stuck on account settings after sign-in, click on link at bottom "Having trouble, open in browser".

4. On My iMac (or My PC) page choose settings for backing up and syncing. You probably should only sync some of your folders. 
  - If you are only syncing some folders, Click "Choose Folders" and add them one-by-one, otherwise simply check box next to Documents and/or Desktop.
  - Click "Change" next to "Backing up all files and folders". Add extensions for large files you don't need synced. For example, files which you'd only use on one computer (big sequence files, files associated with a program only available on one computer [eg. FlowJo]). Suggestions:
    - Raw Sequence files: fastq, fq
    - Compressed files and archives: gz, tar
    - Raw flow cytometry files and FlowJo10 Workspaces: fcs, wsp

5. (optional) Under "Google Drive" section on "My iMac" or "My PC" page, change settings to "Always remove both copies" or "Never Remove Both Copies". Else, you will be frequently interrupted when moving files around. You can always change this later, so may want to keep it as "Ask..." initially.

6. **IMPORTANT**: On "Google Drive" page, you should almost certainly **UNCHECK** the "Sync My Drive to this computer" box. Your files selected in previous section will still sync, but things already on your GCloud Google Drive account will not sync down to your local computer. If you have been backing up to this account from CHPC, for example, you may have many very large files that will needlessly take up space on your desktop (possibly filling your drive and causing issues) AND, ultimately, on Path backup space.

7. Find your files on Google Drive browser interface by logging into your google account on browser. Switch to GCloud account by clicking on your profile pic in top right (usually) and click "Add another account". You can then switch between them in browser. 
  - Files synced from computer are found in a separate section on Google Drive named "Computers" and listed by your computer name. I.e., they are separate from "My Drive".
  - Google Docs now natively opens and saves Microsoft documents. So you can work with Google docs to edit your .docx files, for example, and the files will still be saved as Microsoft documents (unless you explicitly right-click and "Open with.." -> "Google Docs"). 

Observations on Google Backup & Sync:
- It is extremely slow initially as it looks to index files.
- Storage is unlimited, but number of uploads/downloads per day *were* (still are?) limited, so initial syncing could take days.
- Default sharing for Google Drive hosted data is anyone in the University of Utah *with link* can **view**. Worth keeping in mind.

# Statistics
## General stats and graphing good reads:
- [2020 paper on Superplots](https://rupress.org/jcb/article/219/6/e202001064/151717/SuperPlots-Communicating-reproducibility-and) for communicating replicated experiments in graphs. Also a nice bit of discussion on when to use SE versus SD on plots (hint: it depends on what you are trying to show!).
- **Statistics for Biologists Nature Collection** (previously known as the "Points of Significance" article series): A wonderful, ongoing set of short articles on many subjects related to statistical analysis and graphing with special consideration for biologists (i.e. easier to understand) [https://www.nature.com/collections/qghhqm](https://www.nature.com/collections/qghhqm).
- Ten common statistical mistakes: [2019 eLife article](https://elifesciences.org/articles/48175).
- **Modern Statistics for Modern Biology**, by Susan Holmes and Wulfgang Huber: [http://web.stanford.edu/class/bios221/book/index.html](http://web.stanford.edu/class/bios221/book/index.html). Excellent resource with examples in R.
- [Regression models by Brian Caffo](https://leanpub.com/regmods/read#leanpub-auto-preface). Book accompanying Coursera Data Science course with links to videos within.

## Specific methods
### Microbial community-specific methods
- ANCOM for microbial community composion [explained by its author](http://mortonjt.blogspot.com/2016/06/ancom-explained.html)
- [Forum post](https://forum.qiime2.org/t/alpha-and-beta-diversity-explanations-and-commands/2282) succinctly explaining alpha and beta-diversity metrics implemented in Qiime2.
### Multidimensional reduction / ordination
- Ordination with ecology examples specfically. [Excellent post by Michael Palmer](http://ordination.okstate.edu/overview.htm).



