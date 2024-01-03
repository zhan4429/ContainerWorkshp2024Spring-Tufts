## Singularity/apptainer exec
Singularity has a `run` subcommand. This can be used to run the user-defined default command within a container. However, it is recommended to use another subcommand `exec` instead of `run`. If you're interested in `run`, please check the [singularity user guide](https://docs.sylabs.io/guides/3.8/user-guide/cli/singularity_run.html#singularity-run). 

### Syntax
Download or build a container from a given URI. 
```
singularity/apptainer exec [options] image command
```

After we pulled images from public registries onto the cluster, we can use them to run analysis. If you haven't pulled images, please follow the guideline in the [Pull container images](hands-on/pull_run.md).
#### blast
##### syntax
```
blastn –db nt –query nt.fsa –out results.out  
```
