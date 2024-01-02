## Singularity/apptainer pull
### Syntax
Download or build a container from a given URI. 
```
$ singularity pull [output file] <URI>
```

#### Blast from BioContainers
You can find almost all bioinformatics applications from BioContainer's [Package Index](The https://bioconda.github.io/conda-package_index.html)

[Blast](http://blast.ncbi.nlm.nih.gov/Blast.cgi?PAGE_TYPE=BlastDocs) is one of the most popular bioinformatics applications. Here is the guide about how to pull the latest version (2.15.0) from BioContainers and Docker Hub. 

##### Biocontainers
Bioconda is integrated with BioContainers. You can easily find the blast containers from its [bioconda page](https://bioconda.github.io/recipes/blast/README.html#package-blast).

From the instruction, the pull command is listed below:
```
docker pull quay.io/biocontainers/blast:<tag>
(see `blast/tags`_ for valid values for ``<tag>``)
```

You can find the tags by clicking `container` on top of the page as illutrated in the below figure. 

![Biocontainer tags](images/blast1.png)