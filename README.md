# Mastering Reproducibility: pulling, running and building containerized HPC applications

## What is container?  
- A **container** is an abstraction for a set of technologies that aim to solve the problem of how to get software to run reliably when moved from one computing environment to another.  
- A container **image** is simply a file (or collection of files ) saved on disk that stores everything you need to run a target application or applications.  

## Docker  
- The concept of containers emerged in 1970s, but they were not well known until the emergence of Docker containers in 2013.  
- Docker is an open source platform for building, deploying, and managing containerized applications.   
- `Some concerns about the security of Docker containers in HPC`: Docker gives superuser privileges, but we do not want users to have full, unrestricted admin/ root access on production systems.  

## Singularity  
Singularity was developed in 2015 as an open-source project by researchers at Lawrence Berkeley National Laboratory led by Gregory Kurtzer.   
Singularity is emerging as the containerization framework of choice in HPC environments.   
1. Enable researchers to package entire scientific workflows, libraries, and even data.
2. Users do not need to ask their system admin (e.g., RCAC) to install software for them.
3. Can use docker images.
4. Secure! 
5. Does not require root privileges.

## Apptainer
In November 2021, the Singularity project joined the Linux Foundation, and renamed to Apptainer. Besides the name change, there are another three major changes:
1. The command is changed to **apptainer** from **singularity**. But the **singularity** command also works, because it is an alias for the command **apptainer**.
2. The variable prefixes are updated to **APPTAINER_** and  **APPTAINERENV_** instead of the previous **SINGULARITY_** and **SINGULARITYENV_** (although both are accepted).
3. The default Apptainer installation is now unprivileged, **allowing unprivileged users to build containers**. 



## Hands-on
- [Load singularity/apptainer modules](hands-on/load_modules.md)
- [Pull container images](hands-on/pull.md)
- [Run containers](hands-on/run.md)


 

### singularity build  
To build a singularity container, we need to use a computer with elevated privileges, then copy or pull to cluster. To build the container on RCAC clusters, we can build remotely using the [Sylabs Remote Builder](https://cloud.sylabs.io/builder).  

To remotely build an image using singularity, go through the following steps:  
1. Go to: https://cloud.sylabs.io/, and generate a Sylabs account. 
2. Create a new `Access Tokens`, and copy it to clipboard.
3. SSH login to our clusters, and run `singularity remote login` in a terminal and paste the access token at the prompt.
4. Then you can remotely build your own singularity image on the cluster.  

Example: build our own `prokka` container  
[Prokka](https://github.com/tseemann/prokka) is a widely used tool for rapid prokaryotic genome annotation. 

The prokka definition file:
```
BootStrap: docker
From: debian:buster-slim

######################################################################
%post
######################################################################
apt-get -y update
apt-get install -y curl wget nano bzip2 less

# miniconda
mkdir -p /opt
wget https://repo.continuum.io/miniconda/Miniconda3-latest-Linux-x86_64.sh
bash Miniconda3-latest-Linux-x86_64.sh -f -b -p /opt/conda
. /opt/conda/etc/profile.d/conda.sh
conda activate base
conda update --yes --all
conda config --add channels bioconda
conda config --add channels conda-forge
conda create -n prokka prokka==1.14.6


# create bind points for RCAC HPC environment
mkdir /apps /depot /scratch

# clean up
apt-get clean
conda clean --yes --all

######################################################################
%environment
######################################################################
export LC_ALL=C
export PATH=/opt/conda/envs/prokka/bin:$PATH

```

Let's build our first container.  
```
## Generally the login step is needed only once
$ singularity remote login
   ##Paste the access token at the prompt.##

## And build
$ singularity build --remote prokka.sif Inputs/prokka.def 
```

This will take some time, maybe more than 10 mins. After it finishes, you can see information similar to the below information in your terminal:  
```bash
Container URL: https://cloud.sylabs.io/library/zhan4429/remote-builds/rb-618402da4f908fd1f584814c 
INFO:    Build complete: prokka.sif
```

Congratulations for your first self-built container :smiley: :smiley: :+1: :+1:  

Let's test our first prokka container. In the `Inputs` directory, I put a bacteria genome belonging to *Escherichia coli* K12. We can use prokka to annotate this genome.  
```
$ singularity exec prokka.sif prokka --outdir prokka_EcoliK12  --prefix K12 Inputs/Ecoli_K12.fasta
```

Since this is only a small bacterial genome, prokka will finish within several minutes. 
```
[12:20:35] Output files:
[12:20:35] prokka_EcoliK12/K12.gbk
[12:20:35] prokka_EcoliK12/K12.ffn
[12:20:35] prokka_EcoliK12/K12.faa
[12:20:35] prokka_EcoliK12/K12.sqn
[12:20:35] prokka_EcoliK12/K12.gff
[12:20:35] prokka_EcoliK12/K12.fna
[12:20:35] prokka_EcoliK12/K12.fsa
[12:20:35] prokka_EcoliK12/K12.txt
[12:20:35] prokka_EcoliK12/K12.log
[12:20:35] prokka_EcoliK12/K12.err
[12:20:35] prokka_EcoliK12/K12.tbl
[12:20:35] prokka_EcoliK12/K12.tsv
[12:20:35] Annotation finished successfully.
[12:20:35] Walltime used: 4.85 minutes
[12:20:35] If you use this result please cite the Prokka paper:
[12:20:35] Seemann T (2014) Prokka: rapid prokaryotic genome annotation. Bioinformatics. 30(14):2068-9.
[12:20:35] Type 'prokka --citation' for more details.
[12:20:35] Share and enjoy!
```

## Summary
* A Singularity container is just one file (convenient!)
* Use `singularity pull` to download/convert a Singularity or Docker container somebody else built.
* Use `singularity build` (either local or `--remote`) to build your own contaier from a definition file.
* Local builds require elevated privileges (can not do it on the cluster, but can build elsewhere and copy to cluster - because just one file).
* Use `singularity exec` to running your application from the container.
  * Easy: if your native application command is `myapp argument(s)`, then containerized version would be `singularity exec mycontainer.sif myapp argument(s)`
* Complex use cases (MPI, GPUs) are possible, too! Subject of a future _201_ workshop.


Hopufully containers will be useful to your research. 