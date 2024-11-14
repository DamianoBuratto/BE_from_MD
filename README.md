# Binding Energy from MD

This tutorial explains how to compute the binding energy of a ligand-receptor assembly using computational methods. The approach applies to both protein-protein and small molecule-protein assemblies. A practical example is provided using an antibody-antigen system.

The starting point is a well-equilibrated configuration of the assembly at a stable energy minimum. The aim is to sample configurations from this minimum using Molecular Dynamics (MD) simulations and calculate an accurate approximation of the binding energy, including its standard deviation, using the **gmx_MMPBSA** tool. This tutorial demonstrates the implementation of the "single trajectory (linear) MMPBSA" method with **gmx_MMPBSA**. MD simulations will be conducted using **GROMACS**.

---

### Starting Files

You can find these files in the repository.

- **PDB_FILE**: The assembly file in `.pdb` format with chain IDs specified, and the `TER` keyword positioned at the end of every protein chain.  
  Example: `abN48_wt_example.pdb`
  
- **MDP_FILEs**: `.mdp` files containing parameters for the MD simulations. Typically, an MD protocol includes steps for Energy Minimization (EM), NVT equilibration, and NPT equilibration before production runs. Optionally, Simulated Annealing MD (SAMD) can improve sampling before production MD.  
  Examples:
  - `em_amber.mdp`
  - `NVT_amber.mdp`
  - `NPT_amber.mdp`
  - `SAMD_amber.mdp`
  - `PROD_amber.mdp`

- **IN_FILE**: Input file (`.in`) for **gmx_MMPBSA**. The example parameters provided are specific for linear PB calculations and the Amber force field in protein-protein interactions.  
  Example: `MMPBSA_LinearPB.in`

---

### Suggested Procedure

The following is a general workflow, inspired by the custom software **ABiSS**.

0. **Software installation**  
   We are going to use the **GROMACS** software tools to manipulate our system and run MD, **VMD** to visualize and manipulate the system, and **gmx_MMPBSA** to compute the Binding Energy. 

2. **Generate Protein Topology**  
   Use the `pdb2gmx` tool in **GROMACS** to generate the topology for your system. Carefully review the output to ensure:
   - Proper formation of disulfide (S-S) bonds between CYS residues.
   - Correct protonation states of HIS residues (typically at $N\epsilon$).
   - Presence of N-terminal and C-terminal groups at the start and end of protein chains.

3. **Prepare the System**  
   Set up a physiological environment with Periodic Boundary Conditions (PBC):  
   - Create a simulation box and center the system within it.  
   - Add water molecules to the system.  
   - Add ions (e.g., Na+, K+, Cl−) to achieve physiological ionic strength (~150 mM).

4. **Equilibration**  
   - Perform Energy Minimization (EM).  
   - Equilibrate the system using NVT and NPT ensembles (~100 ps each) to achieve the desired temperature and pressure.  

5. **Production MD**  
   - Run Simulated Annealing MD (SAMD) and production simulations. Multiple independent simulations are recommended for robust statistics. This is generally preferable compared to a single-long simulation. 
   - Typical durations:
     - SAMD: ~1 ns  
     - Production: ~4 ns (use the last 2 ns for energy calculations; discard the initial portion to ensure proper equilibration).  
   - Check system stability with RMSD analysis.

6. **Generate Input for gmx_MMPBSA**  
   Process the trajectory and topology files to prepare clean inputs:  
   - **Trajectory (`.xtc`)**: Starting from the production-run .xtc, extract only the protein, remove water and ions, and unwrap PBC.  
   - **Topology (`.top`, `.itp`)**: Keep only the protein and ensure all dependent files are correctly linked.  
   - **TPR file (`.tpr`)**: Starting from the production-run .tpr, create a new `.tpr` file containing only the protein.  
   - **GRO file (`.gro`)**: Generate a protein-only `.gro` file from the production `.tpr` or NPT-equilibrated final frame.  
   - **PDB file (`.pdb`)**: Create a protein-only `.pdb` file with Chain IDs reset.  
   - **Index file (`.ndx`)**: Generate an index file starting from the gro or pdb you just built. Define two groups for the ligand and receptor. The group IDs must match the input for **gmx_MMPBSA**.

7. **Run gmx_MMPBSA**  
   Execute **gmx_MMPBSA** with the prepared input files. The results can be analyzed using the `gmx_MMPBSA_ana` tool, or exported as `.csv` for custom analyses.

---

This guide should help you compute binding energies effectively and reproducibly. Feel free to adapt the process to suit specific systems or objectives.

### Example:


## Links to Tutorials and other Documentations
[Gromacs tutorials](https://tutorials.gromacs.org/)

[Gromacs tutorial by Lemkul](http://www.mdtutorials.com/gmx/)

[gmx_MMPBSA github](https://github.com/Valdes-Tresanco-MS/gmx_MMPBSA)

[gmx_MMPBSA documentation](https://valdes-tresanco-ms.github.io/gmx_MMPBSA/dev/getting-started/)


