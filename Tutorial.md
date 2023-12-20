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

In short, NanoCLUST is a pipeline that is run from the command-line and performs quality control of your 16S rRNA gene sequencing data, and then classifies the reads.

Let's kick things off by installing all the necessary programs NanoCLUST requires to work (prerequisites).

--- 
# Installation
## Prerequisites

NanoCLUST requires Nextflow and Docker or Conda. In this tutorial, we'll use Docker and install Nextflow via Conda. Since Docker is already used on our server, we only need to install Conda.
### Installing Miniconda

Conda is a program designed for managing packages, dependencies, and environments across various programming languages. In simple terms, it helps with the management of installed programs and ensures their compatibility.

Miniconda, a lighter version of Conda, is less complex to install and works perfectly for our goal. Let's get to installing it!

First, we need to create a directory where Miniconda will be installed. It's recommended to locate or create a folder within your home directory for programs. If such a folder doesn't exist, you can create it using this command:

**Tip:** Simply copy the command provided below and paste it into the terminal using your right mouse button. Note that 'CTRL + C/V' does not function within the terminal interface!

```bash
mkdir ~/programs
```

Navigate to the programs directory using this command:
```bash
cd ~/programs
```

Within the programs folder, create a directory specifically for Miniconda:

```bash
mkdir -p miniconda3
```

Next, download the Miniconda installer and start the installation:

**Tip:** When pasting multiple lines at once into the terminal, note that the final line won't execute automatically. You'll need to press 'Enter' for the execution to proceed!

```bash
wget https://repo.anaconda.com/miniconda/Miniconda3-latest-Linux-x86_64.sh -O miniconda3/miniconda.sh
bash miniconda3/miniconda.sh -b -u -p miniconda3
rm -rf miniconda3/miniconda.sh
```

After executing the previous commands; you need to close the terminal and re-open it. This means you have to login again! 
And again, we have to navigate to the right location using: 

```bash
cd programs
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
conda create --name env_nf nextflow=22.10.6
```

When a question pops up ('Proceed ([y]/n)?'): Use the 'y' key on your keyboard to proceed.

To activate the environment; run:

```bash
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

# Preparing Your Sequenced Data
Before diving into NanoCLUST processing for your own sequenced data, let's set the stage! After Nanopore sequencing, your data appears in compressed directories marked with the '.tar.gz' extension. However, NanoCLUST needs files in the '.fastq' format.
To get your data NanoCLUST-ready, you'll need to merge these files. First, locate your sequence data folder. Then, use this command:

```bash
zcat {location} > {new_file_name}.fastq
```

Remember to specify the correct location and assign a name for each merged file based on its directory.

---

# Running NanoCLUST

Alright, it's finally time to fire up NanoCLUST and process our concatenated sequence data! The way NanoCLUST operates depends on specific parameters you can set within the command when running it. But it is always necessary to make sure you're in the right environment (`env_nf`). If you need a refresh on how to do this, refer back to the [testing](https://github.com/BirgitRijvers/NanoCLUST-tutorial/blob/main/Tutorial.md#testing) section of this tutorial.

## Default
To initiate NanoCLUST with default settings, this is the general form of the command:

```bash
nextflow run {main.nf} \ 
             -profile docker \ 
             --reads {'sample.fastq'} \ 
             --db {"db/16S_ribosomal_RNA"} \ 
             --tax {"db/taxdb/"}
```

Simply replace the placeholders within curly brackets with the appropriate file locations. Let's break it down using an example:

Let's say you're in the home directory. Inside, there's a folder named `sequencedata`, housing another folder named `concatenated_sequencedata`, containing the desired `.fastq` file. Also, there's a folder called `programs` where NanoCLUST is installed. The NanoCLUST directory hosts the `main.nf` file and the database directories.

Here's how the paths would look:

- `{main.nf}`: `programs/NanoCLUST/main.nf`
- `{'sample.fastq'}`: `sequencedata/concatenated_sequencedata/barcode1.fastq`
- `{"db/16S_ribosomal_RNA"}`: `programs/NanoCLUST/db/16S_ribosomal_RNA`
- `{"db/taxdb/"}`: `programs/NanoCLUST/db/taxdb`

To run NanoCLUST based on this setup, the command would be:

```bash
nextflow run programs/NanoCLUST/main.nf \ 
             -profile docker \ 
             --reads sequencedata/concatenated_sequencedata/barcode1.fastq \ 
             --db programs/NanoCLUST/db/16S_ribosomal_RNA \ 
             --tax programs/NanoCLUST/db/taxdb
```

This command executes NanoCLUST using the specified paths for the relevant files and directories. Adjust the paths according to your actual file locations before running NanoCLUST.

## Changing the settings

NanoCLUST offers various parameters that you can tweak to alter the output according to your specific requirements. If you want to see all possibilities, check the [list of customizable parameters](https://github.com/BirgitRijvers/NanoCLUST-tutorial/blob/main/Tutorial.md#parameters-overview) in this tutorial. For a detailed explanation, refer to the [NanoCLUST documentation](https://github.com/genomicsITER/NanoCLUST/blob/master/docs/2usage.md).

The required parameters are `-profile`, `--reads`, `--db`, and `--tax`. Other parameters come with default values that are utilized if not explicitly set in the command. To modify these parameters, check the NanoCLUST documentation first. Once you've decided on the changes, simply add the desired parameter to the command.

**Tip:** Use the absolute file paths to for the database locations, this prevents the pipeline from crashing!
Check the [Terminal tips file]() in this repository for more information about absolute and relative paths. 

For instance, let's say you wish to retain NanoCLUST's default settings but adjust the minimum read length required. Here's how you can do it:

```bash
nextflow run programs/NanoCLUST/main.nf \ 
             -profile docker \ 
             --reads sequencedata/concatenated_sequencedata/barcode1.fastq \ 
             --db /user/programs/NanoCLUST/db/16S_ribosomal_RNA \ 
             --tax /user/programs/NanoCLUST/db/taxdb \
             --min_read_length 800
```

### Parameters overview:

A list of all NanoCLUST parameters you can adjust:

- `-profile`
- `--reads`
- `--demultiplex`
- `--demultiplex_porechop`
- `--kit`
- `--umap_set_size`
- `--cluster_sel_epsilon`
- `--min_cluster_size`
- `--polishing_reads`
- `--db`
- `--tax`
- `--min_read_length`
- `--max_read_length`
- `--outdir`
- `--email`
- `--email_on_fail`
- `-name`
- `-resume`
- `-c`
- `--custom_config_base`
- `--max_memory`
- `--max_time`
- `--max_cpus`
- `--plaintext_email`
- `--monochrome_logs`
- `--multiqc_config`

--- 

# Wrap up
Congratulations, you've reached the end of the tutorial! By now, you've successfully installed NanoCLUST, are able to prepare your data and execute the pipeline. To explore the diverse outputs generated, againrefer to the [GitHub page of NanoCLUST](https://github.com/genomicsITER/NanoCLUST). 

I hope NanoCLUST comes in usefull for your projects, and that you feel more confident navigating the command line 😄!
