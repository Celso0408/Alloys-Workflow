# Alloys-Workflow

![plot](./Workflow.png)

When publishing results obtained with SOD Workflow, please consider citing it.


# Colab
* Colab notebook for data visualization [![Open In Colab](https://colab.research.google.com/assets/colab-badge.svg)](https://colab.research.google.com/drive/1asjGKK279q1IFtY9fSB4oAzEqh9cPdl8?usp=sharing)
# Alloys Workflow

In this workflow, the SimStack framework generates configurations to analyze the thermodynamic properties of binary alloys within the Generalized Quasichemical Approximation (GQCA). For this, five different **WaNos** are combined: SOD-2022, Mult-It, UnpackMol, DFT-VASP, and Table-Generator. A table containing the clusters' total energies, degeneracy factors, and several site substitutions is the expected output of this protocol.
 
To create the workflow depicted in **Figure 1**, you must use the drag-and-drop standard procedure of Simstack in five steps. In the first, SOD-2022 **Wano** generates substituted configurations and their respective degeneracy factors. In the second step, we added the Mult-It **Wano** below SOD-2022 to create a tar file of all structures generated by SOD-2022. In the third step, we add the Unpackmol **Wano** inside AdvancedFor loop control to generate the POSCAR files of substituted configurations of the alloy clusters. In the fourth step, we insert the DFT-VASP **Wano** to receive the POSCAR files from the preceding step and execute the DFT calculations. At the end of the protocol, Table-Generator **WaNo** extracts in a yml file the number that identifies a given configuration generated by SOD-2022 with its respective total energy from the OUTCAR file.
 
 ### This workflow allows us to:
```
1. From a given supercell expansion, define the number of substitutions for a given atomic kind and generate their degeneracy factors (SOD-2022).
2. Generate the tar file, which contains all structures with the defined number of atomic substitutions above (Mult-It) and uncompress them (UnpackMol).
2. Run the DFT calculations using VASP code (DFT-VASP).
4. Arrange all the total energy values and degeneracy factors for a given number of atomic substitutions in a yml file at the end of the protocol (Table-Generator). 
```
 
 ## Alloys workflow with **_AdvancedFor_** loop control
 
<img src="https://i.imgur.com/ogc8LsQ.jpg" alt="DFT-Surface WaNo logo" width="100%"/>

**Figure 1** _This workflow aims to perform several DFT calculations of cluster configurations within the GQCA framework. It is made from SOD-2022, Mult-It, UnpackMol, DFT-VASP, and Table-Generator **WaNos** connected by the AdvancedFor loop control. In step 1, we generate the configurations containing a given number of atomic substitutions; another substitutes one atomic species, and their degeneracy factors are calculated using space group symmetry operations from the pristine crystal structure. Steps 2, 3, and 4 define the group of the supercell configurations and set the DFT calculation methods employed in the simulation. In the last step, Table-Generator **Wano** extracts the required variables of the output files from the previous steps._

## 1. Python Setup
To get this workflow up and running on your available computational resources, install the below libraries on Python 3.6 or newer.

```
1. Atomic Simulation Environment (ASE).
2. Python Materials Genomics (Pymatgen).
3. Numpy, os, sys, re, yaml, subprocess.
4. json, csv, shutil, tarfile. 
```

## 2. SOD-2022 Inputs

- INSOD file, containing system definition, supercell size, number of substitutions, and specie replacements, as explained in Site Occupation Disorder (SOD) [software manual](https://github.com/gcmt-group/sod).
- SGO file containing the matrix-vector representations of the symmetry operators associated with the space group of the *parent structure* associated with the alloy. You can create this file using the [Bilbao Crystallographic Server](https://www.cryst.ehu.es/). As explained in [SOD manual](https://github.com/gcmt-group/sod), the first three numbers in each line are one row of the operator matrix, and the fourth number is the component of the operator translation vector.

## 3. SOD-2022 Output

- All information to the next WaNo will be contained in the `calcs.tar` file.

## 4. Mult-It Inputs

- You need to check the box *Structures*.
- In the tarfile field, configure the path `SOD-2022/outputs/calcs.tar` if it is not properly configured.

## 5. Mult-It Outputs

- Configure the' file' name from the `file` command on the top of the AdvancedFor loop, as indicated in **Figure 1**.
- From the *in* command on the top of the loop, configure `list(range(Mult-It.struct_len))`.

## 6. UnpackMol Inputs

- Check the box Multivariable-mode.
- The input-file variable should be set as `Mult-It/outputs/structure_output_dict.yml.`
- Structures-int variable should be set as `${file}.`
- Structures variable should be set as `Mult-It/outputs/Structures.tar.`

## 7. UnpackMol Outputs

- POSCAR files (named as Mol_geom.xyz) should be passed to DFT-VASP **WaNo**.

## 8. DFT-VASP Inputs

- **INCAR tab**: as an option, we can set all INCAR flags available within VASP. However, we expose only a few of them, which is essential for the problem. See the GUI of this WaNo. A brief description of each flag pops up when we hover the mouse over the inputs.
- **KPOINTS tab**: Here, the user can define two types of KPOINTS, `Kpoints_length` and `Kpoints_Monkhorst.`
- **Analysis tab**: Aimed to compute Bader charge analysis and DOS.
- **Files_Run tab**: Mandatory loads the POSCAR file, and as an option, can load INCAR, POTCAR, KPOINTS, and KORINGA files. The KORINGA file can be any file. In the case of this problem, it loads the Input_data.yml file. *Poscar_file* variable should be set as `AdvancedForEach/${file_ITER}/UnpackMol/outputs/Mol_geom.xyz`.

## 9. DFT-VASP Outputs

- OUTCAR file.

## 10. Table-Generator Inputs

- **Imports tab**: Search_in_File variable should be set as vasp_results.yml and import this file using `AdvancedForEach/*/DFT-VASP/outputs/vasp_results.yml` command.
- Search_Parameters: Set the variables `total_energy` and `title`.

## 11. Table-Generator Outputs

- Table-dict.yml containing the variables defined in the Search_Parameters field above, as shown in **Figure 1**.

## Acknowledgements



## License & Copyright
  Developer: Celso Ricardo C. Rêgo, 
  Multiscale Materials Modelling and Virtual Design,
  Institute of Nanotechnology, Karlsruhe Institute of Technology
  https://www.int.kit.edu/wenzel.php

Licensed under the [KIT License](LICENSE).
