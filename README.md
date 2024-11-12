# BE_from_MD

Here we discuss how to compute the binding energy of a ligand-receptor complex using computational methods. This applies to protein-protein or small molecule-protein complexes. I will give you a practical example using an antibody-antigen complex system.

The starting point is a configuration of the complex well equilibrated and in a stable minimum. The idea is to sample configurations of this minima using Molecular Dynamics (MD) and use the frames to obtain a good approximation of the binding energy with a standard deviation using gmx_MMPBSA software. I will show the implementation of ‘single trajectory (linear) MMPBSA’ method using gmx_MMPBSA . The software used for MD is Gromacs.

### Input files:
- **PDB_FILE**: complex file in a .pdb format where chains id are specified and the TER keyword is positioned at the end of every protein
  + e.g. abN48_wt_example.pdb
- __MDP_FILEs__: .mdp files with the parameters used during MD. There will be one mdp file for every MD step. Generally, a good MD protocol requires Energy Minimization (EM), NVT and NPT before production runs. To improve the configuration sampling you can (optionally) add a Simulated Annealing MD before production MD.
  + em_amber.mdp
  + NVT_amber.mdp
  + NPT_amber.mdp
  + SAMD_amber.mdp
  + PROD_amber.mdp 
- **IN_FILE**: .in is the input file used by gmx_MMPBSA. The parameters in the example input file are specific for linear PB and amber forcefield in protein-protein interaction.
  + MMPBSA_LinearPB.in 

### Suggested procedure:


### Example:

