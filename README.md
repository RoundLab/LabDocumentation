<!-- TOC -->

- [CHPC Usage](#chpc-usage)
  - [General](#general)
  - [Starting an interactive shell session on Round Lab node](#starting-an-interactive-shell-session-on-round-lab-node)
  - [Starting interactive session on other partitions.](#starting-interactive-session-on-other-partitions)
  - [Mounting CHPC drives in Mac OS](#mounting-chpc-drives-in-mac-os)
    - [Your Home directory](#your-home-directory)
    - [Round-group shared drives](#round-group-shared-drives)
  - [Using containers with Singularity](#using-containers-with-singularity)
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
- Note that mounting locations can change, so these paths may need to be updated in the future.
- See CHPC guide for additional details: [https://www.chpc.utah.edu/documentation/data_services.php#Direct_mounts](https://www.chpc.utah.edu/documentation/data_services.php#Direct_mounts), including method for mounting on Windows.

### Your Home directory
1. Log in to a terminal session on CHPC.
2. Enter the following command to determine where your home is mounted (replacing with your uNID):
```bash
df | grep "Your_uNID"
```
3. Copy the network drive name. For example for me (and likely all Round lab now) I want: `cottonwood-vg4-0-lv1.chpc.utah.edu`
4. On you Mac connect as you do for Round Lab server:
   1. Go -> Connect to server...
5. Enter server with your uNID as: `smb://ad;uNID@NETWORK-DRIVE-NAME-COPIED-ABOVE/round-home/uNID`

### Round-group shared drives
1. On your mac connect as you do for Round Lab server:
   1. Go -> Connect to server...
2. Enter server name, as below, replacing your uNID:
   1. round-group1: `smb://ad;<uNID>@saltflats-vg6-3-lv1.chpc.utah.edu/round-group1`
   2. round-group2: `smb://ad;<uNID>@cottonwood-vg2-3-lv1.chpc.utah.edu/round-group2`

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
