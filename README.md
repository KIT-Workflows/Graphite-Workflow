<img src="https://raw.githubusercontent.com/KIT-Workflows/Graphite-Workflow/main/graphite_logo.png" alt="graphite WaNo logo" width="200"/>

When publishing results obtained with Graphite-Workflow, please consider citing it.


# DFT-Surface

In this workflow, we use the SimStack framework features to perform as an option a single shot DFT calculation of molecules absorbing on a surface. Here, we combine four different **WaNos**: Mult-Mol, Surface, DFT-VASP, and Table-Generator, to set up a molecule position on the surface, surface type, load molecules file structures, and choose the methods embedded in the DFT approach using VASP code. A table containing the system's total energy, molecule label, and molecule position on the surface is the expected output of this protocol.

Using the drag-and-drop features of Simstack, we can build the Workflow depicted in **Fig 1** in four steps. The Mult-mol **WaNo** accounts for the number of different positions for each molecule on the surface. In the second step, we add the Surface **WaNo** inside the ForEach loop control to generate the POSCAR files of adsorbed molecules to the chosen surface. In the third step, we insert the DFT-VASP **WaNo**, which will receive the generated files from the previous one. At this step, We can take advantage of the parallelization in the HPC remote resources once the ForEach loop control is designed for this end. Table-Generator **WaNo** extracts three variable values on the OUTCAR file: steps two and three output files. This **WaNo** builds a table named Table_var in CSV format at the end of the protocol. 

### In this workflow, we will be able to:
```
1. Load a molecule or a set of them and define the number of points above a particuĺar surface (Mult-Mol).
2. Defining the surface type and setting a molecule's position above the surface (Surface).
3. Run the DFT calculations using VASP code, accounting for the proper corrections (DFT-VASP).
4. Arrange all the total energy values of the system in a table format (Table-Generator). 
```

## DFT-Surface workflow with **_ForEach_** loop control

<img src="Workflow_DFT_Surface.png"  width="90%">

**Fig 1** _This workflow aims to perform several DFT calculations of molecules absorbing on a given surface. It is composed of Mult-Mol, Surface, DFT-VASP, and Table-Generator **WaNos** connected by the ForEach loop control. In step 1, we generate the number of points over the surface, where the molecule will be added. Steps 2 and 3 define the surface type and the DFT calculation methods employed in the simulation. The **WaNo** in the last step extracts the inquired variables of the output file from the previous steps._

## 1. Python Setup
To get this workflow up running on your available computational resources, make sure to have the below libraries installed on Python 3.6 or newer.

```
1. Atomic Simulation Environment (ASE).
2. Python Materials Genomics (Pymatgen).
3. Numpy, os, sys, re, yaml, subprocess.
4. json, csv, shutil, tarfile. 
```
## 2. Mult-Mol Inputs
- Range of the variable position. 
- Number of points in the present in the range. 
- Beginning of the Molecule name, which should appear in all molecules. 
- Directory with the zip file of the molecules.
## 3. Mult-Mol Output
- It should pass all the information to the next WaNo inside the ForEach loop through the `Mult_Mol.iter.*` command on the top of the loop, as Fig 1 shows in step 2.
## 4. Surface Inputs
- Aux_var should be set as `${ForEach_iterator_ITER}` from import workflow variable.
- Mol_name should be set as `Mult_Mol.Molecule_name` from import workflow variable.
- Defining bulk unit cell types, element, and lattice constant.
- Defining slab size, vacuum size, Miller index of the surface, and as an option, set a supercell.
- Check the box when you want to adsorb a molecule on the surface previously defined.
- Setting the molecule distance over the surface and molecule-molecule image distance.
## 5. Surface Output
- POSCAR and Input_data.yml files, which should be passed to DFT-VASP **WaNo**.
## 6. DFT-VASP Inputs
- **INCAR tab**: as an option, we can set all INCAR flags available within VASP. However, we expose only a few of them, which are essential for the problem. See the GUI of this WaNo. A brief description of each flag pops up when we rover the mouse over the inputs.
- **KPOINTS tab**: Here the user can define two types of KPOINTS, `Kpoints_length` and `Kpoints_Monkhorst`.
- **Analysis tab**: Aimed to compute Bader charge analysis and DOS.
- **Files_Run tab**: Mandatory loads the POSCAR file, and as an option can load INCAR, POTCAR, KPOINTS, and KORINGA files. The KORINGA file can be any file. In the case of this problem, it loads the Input_data.yml file.
## 7. DFT-VASP Output
- OUTCAR file.
## 8. Table-Generator Inputs
- Search_in_File: Should be set as OUTCAR and import the OUTCAR file using `ForEach/*/DFT_VASP/outputs/OUTCAR` command.
- Delete_Files: check the box option.
- Search_Parameters: Set the variables `z_0`, `File_number`, and `energy`.  
## 9. Table-Generator Output
- Table-var file in CSV format containing the variables defined in the Search_Parameters field.


## Acknowledgements
This project has received funding from the European Union’s Horizon 2020 research and innovation programme under grant agreement No 957189. The project is part of BATTERY 2030+, the large-scale European research initiative for inventing the sustainable batteries of the future.

## License & copyright
  Developer: Celso Ricardo C. Rêgo, 
  Multiscale Materials Modelling and Virtual Design,
  Institute of Nanotechnology, Karlsruhe Institute of Technology
  https://www.int.kit.edu/wenzel.php

Licensed under the [KIT License](LICENSE).
