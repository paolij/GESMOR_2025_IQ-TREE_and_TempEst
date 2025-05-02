# UF 2025 GSEMOR Workshop
# IQ-TREE and TempEST Tutorial
# Julia Paoli

## Background



### **IQ-TREE Analysis**


1. Set up your folders in HPG

Navigate to the workshop folder in the blue server. Go into your personal folder using the cd command. Make a new folder called IQ-TREE

```bash
cd /blue/general_workshop/<your folder>
```

```bash
mkdir IQ-TREE
```

2. Let's write a script for running IQ-TREE
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
#SBATCH --mail-user=<username>@ufl.edu
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
    -nt AUTO \
    -lmap 20000
```

3. Run your IQ-TREE script
```bash
sbatch iqtree.sh /blue/general_workshop/share/iqtree/TBEV_with_outgroup.fasta
```
4. 