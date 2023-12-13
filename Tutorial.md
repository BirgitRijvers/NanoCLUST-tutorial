#### Teacher
Please make sure the soon to be users of NanoCLUST are added to the dockergroup on the server. This can be done by adding `docker` to the groups parameter when creating accounts, or by running `sudo usermod -aG docker $USER`. 

---
# Introduction
Hi, and welcome to this beginner-friendly tutorial on installing and running NanoCLUST :). 

After completing this tutorial you know how to:
- Install Miniconda
- Set up Nextflow using Conda
- Install NanoCLUST
- Obtain NanoCLUST databases
- Configure NanoCLUST
- Merge raw (.fastq) sequencing files
- Execute NanoCLUST on your samples

For more information check out the [GitHub page of NanoCLUST](https://github.com/genomicsITER/NanoCLUST).

Let's kick things off by installing all the necessary programs NanoCLUST requires to work (prerequisites).

--- 
# Installation
## Prerequisites

NanoCLUST requires Nextflow and Docker or Conda. In this tutorial, we'll use Docker and install Nextflow via Conda. Since Docker is already used on our server, we only need to install Conda.
### Installing Miniconda

Conda is a program designed for managing packages, dependencies, and environments across various programming languages. In simple terms, it helps with the management of installed programs and ensures their compatibility.

Miniconda, a lighter version of Conda, is less complex to install and works perfectly for our goal. Let's get to installing it!

First, we need to create a directory where Miniconda will be installed. It's recommended to locate or create a folder within your home directory for programs. If such a folder doesn't exist, you can create it using this command:
```bash
mkdir ~/programs
```
Navigate to the programs directory. Replace 'programs' with the name of your chosen directory:
```bash
cd ~/programs
```
Within the programs folder, create a directory specifically for Miniconda:
```bash
mkdir -p miniconda3
```
Next, download the Miniconda installer and start the installation:
```bash
wget [https://repo.anaconda.com/miniconda/Miniconda3-latest-Linux-x86_64.sh](https://repo.anaconda.com/miniconda/Miniconda3-latest-Linux-x86_64.sh) -O miniconda3/miniconda.sh

bash miniconda3/miniconda.sh -b -u -p miniconda3

rm -rf miniconda3/miniconda.sh
```
Now we only need to initialize miniconda by running these commands:
```bash
miniconda3/bin/conda init bash

conda config --add channels defaults
conda config --add channels bioconda
conda config --add channels conda-forge
conda config --set channel_priority strict
```
### Installing Nextflow with Conda

Now that Miniconda is installed, we can use it to install Nextflow.

Nextflow is the programming language in which NanoCLUST is written. It's designed for running pipelines and is based on several other programming languages.

Conda works with environments, allowing the installation of various programs in each environment created. For now, we only require Nextflow, so let's create an environment containing Nextflow. We'll use an older version because NanoCLUST isn't compatible with the newest versions.
```bash
# Newer versions are not compatible with NanoCLUST
conda create --name env_nf nextflow=22.10.6

conda activate env_nf
```
---
## NanoCLUST
### Installation
Now that all the prerequisites are in place, let's proceed with the installation of NanoCLUST! The program is available on GitHub, making it easy to clone the contents directly. First, navigate to the directory where you want to install the program. I suggest using the 'programs' directory, which we've previously created. You can confirm your current location with the command `pwd`. If it doesn't display the desired directory, switch to it using `cd programs`. Change 'programs' to another directory name if you prefer installing the program there. 

Clone the GitHub repository using this command:
```bash
git clone https://github.com/genomicsITER/NanoCLUST.git
```
This command will fetch the necessary files and set up NanoCLUST in your chosen directory.
### Databases
To classify our reads, NanoCLUST compares our data to two databases. As these databases are not included in the GitHub contents, we need to install them. We'll create the databases within the NanoCLUST directory. Start by changing your location to that directory using this command:
```bash
cd NanoCLUST
```
Next, create a new directory where the databases will be placed, and change your location to the fresh directory:
```bash
mkdir db
cd db
```
Download the first database using the following command:
```bash
wget https://ftp.ncbi.nlm.nih.gov/blast/db/16S_ribosomal_RNA.tar.gz && tar -xzvf 16S_ribosomal_RNA.tar.gz
```
NanoCLUST also requires a database containing taxonomic names for each of the numerical taxids it identifies. Create a new location for this database within the `db` directory, change your location to it, and download the database:

```bash
mkdir taxdb
cd taxdb
wget https://ftp.ncbi.nlm.nih.gov/blast/db/taxdb.tar.gz && tar -xzvf taxdb.tar.gz
```
These commands will download and extract the necessary databases required by NanoCLUST.
### Alter code
Unfortunately, the NanoCLUST code doesn't work straight away, there is a little bug in the code that we need to fix. We need to change a line in the *nextflow.config* file in the NanoCLUST directory. We can do that in multiple ways, but let's do it using nano! First, we need to move ourselves out of the taxdb folder, and into the main NanoCLUST folder. We do this by using the `cd` command once again, and add 2 dots after the command. This tells the terminal to move you to the directory directly above the one you are in. 
```bash
cd ..
cd ..
```
The line that we have to change is line 104; it originally contains:
`withName: consensus_classification { container = 'hecrp/nanoclust-consensus_classification'`
But we want to change it to:
`withName: consensus_classification { container = 'ncbi/blast:latest'`
There are multiple ways to do this, but let's use this command:
```bash
sed -i "104s|withName: consensus_classification { container = 'hecrp/nanoclust-consensus_classification'|withName: consensus_classification { container = 'ncbi/blast:latest'|" nextflow.config
```
---
# Testing
Assuming all previous steps completed without issues, it's time to test NanoCLUST! The tool provides a built-in test profile for quick validation. Before running NanoCLUST, ensure you are in the correct Conda environment where Nextflow was installed: 'env_nf'. You can verify this by checking the first word displayed on your command line prompt. If it shows '(base)', activate the environment by running `conda activate env_nf`. If it displays '(env_nf)', you're all set!

Now, execute the test using the following command:
```bash
nextflow run main.nf -profile test,docker
```

During execution, several prompts will appear on your screen. Look for this message upon completion:
```bash
[nf-core/nanoclust] Pipeline completed successfully
WARN: [nf-core/nanoclust] Could not attach MultiQC report to summary email
Completed at: ?
Duration    : ?
CPU hours   : ?
Succeeded   : 52
```

If you encounter this message, your test was successful! Congratulations, NanoCLUST has been successfully installed!

---
# Running
