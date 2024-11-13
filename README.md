# BindingEenergy from MD

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
The following is a general procedure used in my custom software ABiSS. 

The first step is to generate the topology of your protein given a ForceField. Generally, pdb2gmx Gromacs tool will take care of generating the topology, but you must double-check its work. In particular, you need to confirm the formation of S-S bonds between CYS, the protonation state of the HIS (you generally want to keep them at $N\epsilon$ protonation state), and the presence of C-terminal and N-terminal at the start/end of every protein chain.

The second step is to put the system in a physiological environment with Periodic Boundary Conditions (PBC). This means:
1. Create a box and move your system to the center of it
2. add water to the system
3. add ions to the system (generally a mix of K or Na and Cl) to reach a physiological concentration (generally 150nM)


To have a good estimation of binding energy with its standard deviation it is preferable to run multiple simulations in parallel. 

### Example:

