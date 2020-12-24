# GROMACS_TUTORIAL <br>

## Gromacs commands for simulating protein-protein complex in solvent <br>

### Requirements: <br>
#### *Install Xmgrace for generating graphs from .xvg files <br>

###  > Create a file containing all the essential parameter files including the processed .pdb file <br>

###  > Open terminal direct to the PWD which contains all the essential parameter files <br>

## Step 1: Generating protein topology files <br>

"gmx pdb2gmx -f complex.pdb -o complex_processed.gro -water spce" <br>

> Type 15 at the command prompt, followed by 'Enter <br>

> Additional options: in case of missing atoms use -missing, in case of other error use -ignh to ignore <br>

## Step 2: Define unit cubical cell/box of 1 Amstrong <br>

"gmx editconf -f complex_processed.gro -o complex_newbox.gro -c -d 1.0 -bt cubic" <br>

## Step 3: Adding solvent (water) <br>

"gmx solvate -cp complex_newbox.gro -cs spc216.gro -o complex_solv.gro -p topol.top" <br>

## Step 4: Adding ions <br>

"gmx grompp -f ions.mdp -c complex_solv.gro -p topol.top -o ions.tpr" <br>

## Step 5: Setting the net charge zero <br>

"gmx genion -s ions.tpr -o complex_solv_ions.gro -p topol.top -pname NA -nname CL -neutral" <br>

## Step 6: Energy minimization <br>

"gmx grompp -f minim.mdp -c complex_solv_ions.gro -p topol.top -o em.tpr" <br>

"gmx mdrun -v -deffnm em" <br>

## Step 7: Generate potential energy graph# <br>

"gmx energy -f em.edr -o potential.xvg" <br>

### > At the prompt, type "10 0" to select Potential (10); zero (0) terminates input. <br>

"xmgrace potential.xvg" <br>
 
## Step 8: NVT equilibration <br>

"gmx grompp -f nvt.mdp -c em.gro -r em.gro -p topol.top -o nvt.tpr" <br>

"gmx mdrun -deffnm nvt" <br>

"gmx energy -f nvt.edr -o temperature.xvg" <br>

##  > At the prompt, type "16 0" <br>

##  > xmgrace temperature.xvg <br>
 
## Step 9: NPT  equilibration <br>

"gmx grompp -f npt.mdp -c nvt.gro -r nvt.gro -t nvt.cpt -p topol.top -o npt.tpr" <br>

"gmx mdrun -deffnm npt" <br>

"gmx energy -f npt.edr -o pressure.xvg" <br>

###  > At the prompt, type "18 0" <br>

"gmx energy -f npt.edr -o density.xvg" <br>

###  > At the prompt, type "24 0" <br>

 
## Step 10: Production MDrun <br>

"gmx grompp -f md.mdp -c npt.gro -t npt.cpt -p topol.top -o md_0_1.tpr" <br>

## Step 10: Execution of MD <br>

### > either: <br>

"gmx mdrun -deffnm md_0_1" <br>

### > or <br>

"gmx mdrun -deffnm md_0_1 -nb gpu" <br>

