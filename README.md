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

Once you generate your system, you need to run energy minimization and equilibrate it (EM, NVT, NPT). The equilibration process (NVT and NPT) is generally very fast (~100ps) and is needed to have a system starting at the correct Temperature and Pressure.

Using your equilibrated system, you can run SAMD and Production simulations. To have a good estimation of binding energy with its standard deviation it is preferable to run multiple simulations in parallel. For this reason is suggested to run multiple SAMD+Production simulations in parallel. SAMD is generally fast (~1ns) while Production can be a bit longer (~4ns). The last part of the Production simulation (~2ns) for every run will be used to compute the Binding Energy. The first part of the simulation is usually discarded because the system will still require a bit of time to reach an optimal equilibrium (you can check that with the RMSD).

At this point, you need to manipulate your trajectory (.xtc file), topology (.top and .itp files), and tpr of the production run (.tpr file) to generate the input files for gmx_MMPBSA. Generally, you want to feed to gmx_MMPBSA a very clean version of your system. You will need multiple input files:
- **trajectory .xtc**: starting from the production .xtc trajectory, you need to keep only the protein (remove water and ions) and remove the PBC.
- **topology .itp**: starting from the topology of your system, you need to keep only the protein. All the .itp files must follow the topology if you change the folder for the calculations.
- **tpr file**: starting from the .tpr file of the production run, you need to generate a new tpr where you have only the protein.
- **gro file**: starting from the .tpr of the production run or final gro of NPT run, you need to generate a new gro file with only the protein.
- **pdb file**: starting from the .tpr of the production run or final gro of NPT run, you need to generate a new pdb file with only the protein. You also need to reset the ChainID information in this file.
- **index file**: starting from the gro or pdb file you just generated, you need to build an index file containing two groups that define your ligand and your receptor. The number of these two groups has to be specified in the input of gmx_MMPBSA

Once all your files are ready, you can run gmx_MMPBSA, the output can be analyzed with their gmx_MMPBSA_ana tool or you can specify a .csv file as output that can be used for custom analysis.

### Example:


## Links to Tutorials and other Documentations
[Gromacs tutorials](https://tutorials.gromacs.org/)

[Gromacs tutorial by Lemkul](http://www.mdtutorials.com/gmx/)

[gmx_MMPBSA github](https://github.com/Valdes-Tresanco-MS/gmx_MMPBSA)

[gmx_MMPBSA documentation](https://valdes-tresanco-ms.github.io/gmx_MMPBSA/dev/getting-started/)


