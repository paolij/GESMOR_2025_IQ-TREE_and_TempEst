# UF 2025 GSEMOR Workshop
# IQ-TREE and TempEST Tutorial
# Julia Paoli

## IQ-TREE Background

We will use the free and open-source software IQ-TREE to reconstruct phylogenies using the maximum likelihood method. IQ-TREE is a fast and efficient tool to construct phylogenetic trees. We will discuss features of IQ-TREE version 2 below. 

The data we will analyze comes from a surveillance study of ticks collected in Mongolia. We collected *I. persulcatus* ticks (n = 1300) in May 2020. Viral RNA was extracted from cell cultures, converted to cDNA, and library prepped. We conducted whole-genome shotgun sequencing on the Illumina iSeq100 platform. To read more about this study, see the paper [here](https://www.mdpi.com/2076-0817/13/12/1086).

**Maximum Likelihood**

There are several methods to reconstruct phylogenies from sequence data including distance-based methods (ex Neighbor-Joining), maximum parsimony, and maximum likelihood. 

Briefly, the maximum likelihood (ML) method identifies the tree topology which best explains the observed sequence data given a specified evolutionary model. To read more about the ML approach see this [paper](https://link.springer.com/article/10.1007/BF01734359).

**Model Selection**

IQ-TREE supports many different nucleotide and amino acid substitution models. For a full list, see [here](https://iqtree.github.io/doc/Substitution-Models). 

If you are not sure which model to pick for your data, IQ-TREE offers the ModelFinder option which automatically determines the best-fit model for your data. To read more about ModelFinder, see the paper [here](https://www.nature.com/articles/nmeth.4285). 

**Likelihood Mapping**

IQ-TREE 2 features the ability to conduct the [likelihood mapping approach](https://www.pnas.org/doi/full/10.1073/pnas.94.13.6815) (-lmap option). Likelihood mapping quantifies the phylogenetic signal in an input alignment by comparing the likelihood of tree topologies for groups of randomly drawn four sequences (quartets). Likelihood maps represented by triangles provide a visual representation of the distribution of resolved and unresolved quartets. A high degree of resolved quartets (found in triangle corners) indicates data sufficiently robust for phylogenetic inference. Likelihood mapping results are given in the .iqtree report file and the likelihood mapping plots are printed to .lmap.svg. For more information about the applications of likelihood mapping, see [paper here](https://publichealth.jmir.org/2020/2/e19170/).

**Ultrafast Bootstrap Values**

Ultrafast bootstrap values are statistical measures used to assess the reliability of branches in a phylogenetic tree. Like traditional bootstrap methods, they estimate how well the data support a given tree topology by resampling the data and reconstructing trees repeatedly. However, ultrafast bootstrapping (UFBoot) offers a much faster and more unbiased alternative to standard bootstrap procedures. For further reading, the paper is [here](https://academic.oup.com/mbe/article/35/2/518/4565479?login=false).

UFBoot values range from 0 to 100 and are interpreted as the percentage of times a branch appears in the resampled trees. Higher values (>90) indicate stronger support for a given branch.

**Parameters**

IQ-TREE offers many parameters for analyzing your data. Let's review some commonly used ones. For a full list of options, see [command reference](https://iqtree.github.io/doc/Command-Reference#general-options). 


| Parameter       | Description                                                                 |
|-----------------|-----------------------------------------------------------------------------|
| `-s`            | Specifies the input alignment file (usually FASTA format).                |
| `-m`            | Specifies the substitution model if you know which one.                    |
| `-m MFP`        | Automatically selects the best-fit substitution model for the data.        |
| `-bb`           | Performs ultrafast bootstrap analysis with the specified number of replicates. |
| `-nt AUTO`      | Automatically selects the optimal number of cores to use based on the data.|
| `-lmap`         | Specifies the number of quartets to be randomly drawn for likelihood mapping analysis. |

**Output Files**

IQ-TREE generates several types of output files. Let's take a look at the main ones:

| Output File Suffix | Description                                                                 |
|--------------------|-----------------------------------------------------------------------------|
| `.treefile`        | The ML tree in NEWICK format.                                              |
| `.iqtree`          | The main output report file. Contains computational results.               |
| `.log`             | Log file of the entire run. Specifies any errors encountered.              |
| `.model.gz`        | Lists all substitution models tested and their scores.                    |
| `.lmap.svg`        | Visualization of likelihood mapping analysis.                              |



**Further Resources**

1. [IQ-TREE 2 paper](https://academic.oup.com/mbe/article/37/5/1530/5721363)
2. [IQ-TREE manual](http://www.iqtree.org/doc/iqtree-doc.pdf)
3. [IQ TREE website](https://iqtree.github.io/)
4. [IQ TREE webserver](http://iqtree.cibiv.univie.ac.at/)
5. [FigTREE website](https://tree.bio.ed.ac.uk/software/figtree/)

### IQ-TREE Analysis


1. **Set up your folders in HPG**

Navigate to the workshop folder in the blue server. Go into your personal folder using the cd command. Make a new folder called iqtree.

```bash
cd /blue/general_workshop/<your folder>
```

```bash
mkdir iqtree
```

2. **Let's write a script for running IQ-TREE**

```bash
nano iqtree.sh
```

```bash
#!/bin/bash
#SBATCH --job-name=IQ-TREE
#SBATCH --mem=20G
#SBATCH --time=24:00:00
#SBATCH --nodes=1
#SBATCH --cpus-per-task=1
#SBATCH --account=general_workshop
#SBATCH --qos=general_workshop
#SBATCH --mail-user=<username>@ufl.edu # Replace with your UF email
#SBATCH --mail-type=ALL

module load iq-tree/2.2.2.7

## Usage
# sbatch iqtree.sh <path_to_fasta_file>

## Script

# Input Fasta File
FASTA=$1

# Extract the base name of the FASTA file without path and extension
BASE_NAME=$(basename "$FASTA" | sed 's/\..*//')

# Run IQ-TREE
iqtree -s "${FASTA}" \
    -m MFP \
    -bb 1000 \
    -nt 1 \
    -lmap 20000
```

3. **Run your IQ-TREE script**
```bash
sbatch iqtree.sh /blue/general_workshop/share/iqtree/TBEV_with_outgroup.fasta
```

4. **Download your output files**

You should now download all of the output files generated by IQ-Tree. Save these to a folder for future reference. 

5. **Visualize your ML Tree in FigTree**

We can visualize our ML tree using software like FigTree. You should have downloaded FigTree for the workshop. If not, the download link is [here](https://github.com/rambaut/figtree/releases).

Open the TBEV_with_outgroup.treefile. This should automatically open in FigTree. If not, right click on the file to select FigTree. 

When you first open the file in FigTree, you should see an unrooted tree. FigTree allows  allows users to perform midpoint rooting or to outgroup root by user selection. 

In this tutorial, we have just inferred a phylogenetic tree with Orthoflavivirus Omsk hemorrhagic fever virus (OHFV) included as an outgroup (AY193805.1). In FigTree, find the tip labeled AY193805.1, click on it, then click ‘Reroot’ in the top menu bar.
To show tips in ascending order, in the left panel select Trees &rarr; order nodes.

To show the ultrafast branch labels, in the left panel select Branch Labels &rarr; Display label.

## TempEST Background

TempEST is a free and open-source software allowing for the exploration of temporal signal of a data set. You can download it [here](https://tree.bio.ed.ac.uk/software/figtree/) and read the paper [here](https://academic.oup.com/ve/article/2/1/vew007/1753488?login=false).

TempEST evaluates how well a dataset conforms to a molecular clock model—an assumption that genetic mutations accumulate at a relatively constant rate over time. Under this model, the genetic divergence between sequences is proportional to the time since their most recent common ancestor. TempEST assesses this by comparing genetic distances with sampling dates to determine whether the data exhibit a clock-like signal, which is essential for accurate time-scaled phylogenetic analysis.

To assess the robustness of the temporal signal, TempEST generates a plot of root-to-tip genetic distance against sampling date. A positive, linear relationship indicates that the data are clock-like and suitable for time-scaled phylogenetic inference. Conversely, if the points are scattered without a clear trend, the data may not be reliable for such analyses. Running a TempEST analysis before conducting BEAST can help determine whether your dataset has sufficient temporal signal for time-based phylogenetic inference.

### TempEST Analysis

For our TempEST analysis, we will follow the tutorial available on the [BEAST website](https://beast.community/tempest_tutorial). 

Please navigate to that site so we can follow the instructions together.
