# Metagenomics/Metaproteomics Snakemake Workflow

In this repository, we provide a modular and reproducible Snakemake pipeline for our **metagenomic** and **metaproteomic** data analysis. The workflow supports steps such as read quality control, host-sequence removal, assembly, binning, bin quality checks, taxonomic profiling, gene calling, protein annotation, and more.

## Table of Contents

1. [Overview](#overview)  
2. [Workflow Highlights](#workflow-highlights)  
3. [Project Structure](#project-structure)  
4. [Installation & Dependencies](#installation--dependencies)  
5. [Usage](#usage)  
   - [Local Execution](#local-execution)  
   - [HPC/Cluster Execution](#hpccluster-execution)  
   - [Containerized Execution](#containerized-execution)  
6. [Configuration](#configuration)  
7. [Adding/Modifying Rules](#addingmodifying-rules)  
8. [Contributing](#contributing)  
9. [License](#license)  
10. [Acknowledgements](#acknowledgements)

---

## Overview

Our aim is to make this pipeline available to the scientific community (and anyone else interested), providing an easy way to streamline the often complex multi-step analyses of metagenomic/metaproteomic datasets through a robust, modular, and reproducible framework. By leveraging Snakemake and Docker/Singularity containers:

- **Modular**: Each analysis step (e.g., QC, Taxonomic Profiling, Assembly, Binning) is managed by separate rule files.  
- **Scalable**: Seamlessly parallelize jobs on local machines or HPC clusters.  
- **Reproducible**: Environments are controlled via containers (Docker/Singularity).  
- **Maintainable**: Clear directory and file structure for inputs, outputs, and logs.

---

## Workflow Highlights

1. **QC**: Uses standard tools (FastQC & MultiQC) to assess read quality.  
2. **Host-Sequence Removal**: Aligns reads to a reference (Mus musculus genome) to remove contamination.  
3. **Assembly**: Performs de novo assembly (MEGAHIT, SPAdes).  
4. **Binning**: Groups assembled contigs into MAGs (MetaBAT).  
5. **Bin Quality Check**: (CheckM).  
6. **Taxonomic Profiling**: (MetaPhlAn4).  
7. **Gene Calling**: (Prodigal).  
8. **Protein Annotation**: (eggNOG).  
9. **MAG Classification**: (GTDB-Tk).

---

## Project Structure

Below is an example of how the repository is organized:

```bash
.
├── Snakefile                      # Minimal top-level Snakefile
├── config
│   └── config.yaml                # Global pipeline configuration (paths, parameters)
├── data
│   ├── raw                        # Raw input data (FASTQ files, etc.)
│   └── processed                  # Intermediately processed data (optional)
├── logs                           # Log files for each step, grouped by rule/sample
├── reports                        # Final summary reports, e.g., MultiQC
├── resources
│   ├── databases                  # Reference DBs
│   └── reference_genome           # Host or reference genome(s)
├── results
│   ├── assembly
│   ├── binning
│   ├── binning-qc
│   ├── gene-calling
│   ├── host-sequence-removal
│   ├── mag-classification
│   ├── protein-annotation
│   └── qc
├── workflow
│   ├── Snakefile                  # Main pipeline logic (includes rules/*.smk)
│   ├── envs                       # Conda environment YAMLs
│   ├── rules
│   │   ├── alignment.smk
│   │   ├── assembly.smk
│   │   ├── binning.smk
│   │   ├── bin-quality-check.smk
│   │   ├── qc.smk
│   │   └── ...
│   ├── scripts                    # Custom scripts used in different rules
│   └── containers                 # Dockerfiles
└──
```

# Installation and Dependencies

1. Clone repository 

```bash
$ git clone https://github.com/polito-uniupo-bioinformatics/ad-shotgun-metaproteomics-worklow.git
$ cd metaproteomics-pipeline
```

2. Install Snakemake:
- Conda (recommended)
```bash
$ conda create -n snakemake_env -c conda-forge -c bioconda snakemake
$ conda activate snakemake_env
```
- Via pip:
```bash
$ pip install snakemake
```

## Usage

1.	Prepare Input Data: Place raw FASTQ files in data/raw/. Update config.yaml to reflect filenames, sample IDs, etc. 
2. Execute the Pipeline:
```bash
$ snakemake --cores 4
```
This runs all rules in parallel using up to 4 CPU cores. Adjust as needed.

## HPC/Cluster Execution
1. Cluster Configuration: Create or update a cluster profile (e.g., SLURM, SGE). For instance, a profile in profiles/slurm/ might define resources for each rule. 
2. Submit to Cluster:
```bash
$ snakemake --profile profiles/slurm --use-conda
```
## Resource Binding: 
Ensure references and data are accessible on the cluster. You can also mount HPC scratch directories if using Singularity.


## Containerized Execution
You can run this workflow fully containerized:
- Multiple Containers (Rule-Specific)
- Each rule references a smaller dedicated container. 
- Example (in assembly.smk):
```Python
rule assembly:
  container: "docker://youruser/assembly:latest"
  ...
```

- Run with:
```bash
$ snakemake --use-singularity --cores 4
```

## Configuration

config.yaml: Contains top-level settings like reference genome paths, tool parameters, or sample lists.

- Example:
```yaml
samples:
  - sample1
  - sample2

resources:
  reference_genome: "resources/reference_genome/Mus_musculus.GRCm39.dna.primary_assembly.fa.gz"

assembly:
  tool: "megahit"
  threads: 8
  memory: "16G"
```

Contributing
1. Fork and Clone this repo.
2.	Create a Branch for your feature/fix.
3.	Implement your changes (new rules, improvements, etc.).
4.	Open a Pull Request with a clear description of changes.
5.	We encourage tests against small example datasets.

## License

This project is licensed under the MIT License — feel free to adapt or change to your specific license of choice.

## Acknowledgements
This Project was supported By: NextGeneration EU PRIN 2022 PNRR Prot. P2022AFS8P



<p>
  <img src="Acks/next-gen-eu.png" alt="img.png" width="110" height="100" />
  <img src="Acks/muir-italy.png" alt="img_1.png" width="110" height="100" />
  <img src="Acks/Polito.png" alt="img_2.png" width="110" height="100" />
  <img src="Acks/UniUPO.png" alt="img_3.png" width="110" height="100" />
</p>


## Questions or Suggestions?

Please open an issue or submit a pull request if you want to propose improvements or report bugs. We’re open to collaboration to enhance the pipeline’s functionality and maintainability.

For direct contact, feel free to email any of the following:

- **M. Salim Dason**: [mohammed.dason@polito.it](mailto:mohammed.dason@polito.it)  
- **Francesco Favero**: [francesco.favero@med.uniupo.it](mailto:francesco.favero@med.uniupo.it)  
- **Professor Davide Cora**: [davide.cora@uniupo.it](mailto:davide.cora@uniupo.it)

