# NGI-ChIPseq
Nextflow ChIP-seq data analysis pipeline, National Genomics Infrastructure, Science for Life Laboratory
in Stockholm, Sweden

Authors: Chuan Wang (@chuan-wang) and Phil Ewels (@ewels)

# UNDER DEVELOPMENT

This pipeline is currently under active development and has very little in the way of testing. You may well have problems running it. Any help debugging is very welcome! Please fork, make changes and submit a pull request.

## Installation
### NextFlow installation
See https://github.com/SciLifeLab/NGI-NextflowDocs for instructions on how to install and configure Nextflow.

### Pipeline installation
This pipeline itself needs no installation - NextFlow will automatically fetch it from GitHub when run if
`SciLifeLab/NGI-ChIPseq` is specified as the pipeline name.

If you prefer, you can download the files yourself from GitHub and run them directly:
```
git clone https://github.com/SciLifeLab/NGI-ChIPseq.git
nextflow run NGI-ChIPseq/main.nf
```

## Configuration
By default, the pipeline is configured to run on the Swedish UPPMAX cluster (milou / irma).

You will need to specify your UPPMAX project ID when running a pipeline. To do this, use the command line flag `--project <project_ID>`.

To avoid having to specify this every time you run Nextflow, you can add it to your personal Nextflow config file instead. Add this line to `~/.nextflow/config`:

```
params.project = 'project_ID'
```

The pipeline will exit with an error message if you try to run it pipeline with the default UPPMAX config profile and don't set project.

## Running using Docker

We are in the process of setting up a Docker image with the pipeline requirements, for easier use and better reproducibility. Check back soon for more information on this!

## Running on other clusters

It is entirely possible to run this pipeline on other clusters, though you will need to set up your own config file so that the script knows where to find your reference files and how your cluster works.

Copy the contents of `conf/uppmax.config` to your own config file somewhere and then reference it with `-c` when running the pipeline.

If you think that there are other people using the pipeline who would benefit from your configuration (eg. other common cluster setups), please let us know. It should be easy to create a new config file in `conf` and reference this as a named profile in `nextflow.config`. Then these configuration options can be used by specifying `-profile <name>` when running the pipeline.

## Running the pipeline
The typical command for running the pipeline is as follows:

```
nextflow run SciLifeLab/NGI-ChIPseq --reads '*_R{1,2}.fastq.gz' --macsconfig 'macssetup.config'
```

### `--reads`
Location of the input FastQ files:

* Single-end reads:
```
 --reads 'path/to/data/*.fastq.gz'
```

* Paired-end reads:
```
 --reads 'path/to/data/sample_*_{1,2}.fastq.gz'
```

**NB: Must be enclosed in quotes!**

Note that the `{1,2}` parentheses are required to specify paired end data. Running `--reads '*.fastq'` will treat
all files as single end. The file path should be in quotation marks to prevent shell glob expansion.

If left unspecified, the pipeline will assume that the data is in a directory called `data` in the working directory.


### `--macsconfig`
The setup file for peak calling using MACS. For example, the input FastQ files for your ChIP samples and control samples are `ChIPSampleID{1,2,3}.fastq.gz` and `CtrlSampleID{1,2}.fastq.gz`, respectively. ChIP-seq peak calling will be done in the following setup:

* In `AnalysisID1`: peaks called by comparing `ChIPSampleID1` with `CtrlSampleID1`
* In `AnalysisID2`: peaks called by comparing `ChIPSampleID2` with `CtrlSampleID2`
* In `AnalysisID3`: peaks called with single-sample `ChIPSampleID3` without control

The corresponding config file should be as follows:
```
ChIPSampleID1,CtrlSampleID1,AnalysisID1
ChIPSampleID2,CtrlSampleID2,AnalysisID2
ChIPSampleID3,,AnalysisID3
```

### `--genome`
The reference genome to use of the analysis, needs to be one of the genome specified in the config file.
The human `GRCh37` genome is used by default.
```
--genome 'GRCh37'
```
See [`conf/uppmax.config`](conf/uppmax.config) for a list of the supported reference genomes and their keys. Common genomes that are supported are:

* Human
  * `--genome GRCh37`
* Mouse
  * `--genome GRCm38`
Check the config file for more supported reference genomes.

### `--outdir`
The output directory where the results will be saved.

### `--rlocation`
Some steps in the pipeline run `R` with required modules. By default, the pipeline will install these modules to `~/R/nxtflow_libs/` if not present. You can specify what path to use with this command line flag.

### `-c`
Specify the path to a specific config file (this is a core NextFlow command). Useful if using different UPPMAX projects or different sets of reference genomes. **NB:** one hyphen only (core Nextflow parameter).
