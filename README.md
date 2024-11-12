# BE_from_MD

Here we discuss how to compute the binding energy of a ligand-receptor complex using computational methods. This applies to protein-protein or small molecule-protein complexes. 
The starting point is a configuration of the complex well equilibrated and in a stable minimum. The idea is to sample configurations of this minima using Molecular Dynamics (MD) and use the frames to obtain a good approximation of the binding energy with a standard deviation using gmx_MMPBSA software. I will show the implementation of ‘single trajectory MMPBSA’ method using gmx_MMPBSA . The software used for MD is Gromacs.
INPUT FILES:
PDB_FILE -> complex file in a .pdb format where chains id are specified and the TER keyword is positioned at the end of every protein
MDP_FILEs -> mdp files with the parameters used during MD. There will be one mdp file for every MD step. Generally, a good MD protocol requires Energy Minimization (EM), NVT and NPT before production runs. To improve the configuration sampling you can (optionally) add a Simulated Annealing MD before production MD.
IN_FILE -> is the input file used 
