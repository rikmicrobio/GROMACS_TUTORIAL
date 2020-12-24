#GROMACS_TUTORIAL <br>

## Gromacs commands for simulating protein-protein complex in solvent <br>

# Requirements: <br>
#*Install Xmgrace for generating graphs from .xvg files <br>

# * Create a file containing all the essential parameter files including the processed .pdb file <br>

# * open terminal direct to the PWD which contains all the essential parameter files <br>

## Step 1: Generating protein topology files

gmx pdb2gmx -f complex.pdb -o complex_processed.gro -water spce

> Type 15 at the command prompt, followed by 'Enter

> Additional options: in case of missing atoms use -missing, in case of other error use -ignh to ignore#

## Step 2: Define unit cubical cell/box of 1 Amstrong

"gmx editconf -f complex_processed.gro -o complex_newbox.gro -c -d 1.0 -bt cubic"

## Step 3: Adding solvent (water)#

"gmx solvate -cp complex_newbox.gro -cs spc216.gro -o complex_solv.gro -p topol.top"

## Step 4: Adding ions

"gmx grompp -f ions.mdp -c complex_solv.gro -p topol.top -o ions.tpr"

## Step 5: Setting the net charge zero

"gmx genion -s ions.tpr -o complex_solv_ions.gro -p topol.top -pname NA -nname CL -neutral"

## Step 5: Energy minimization

"gmx grompp -f minim.mdp -c complex_solv_ions.gro -p topol.top -o em.tpr"

"gmx mdrun -v -deffnm em"

#Step 6: Generate potential energy graph#

gmx energy -f em.edr -o potential.xvg

At the prompt, type "10 0" to select Potential (10); zero (0) terminates input.

xmgrace potential.xvg
 
#step 7: NVT equilibration#

gmx grompp -f nvt.mdp -c em.gro -r em.gro -p topol.top -o nvt.tpr

gmx mdrun -deffnm nvt

gmx energy -f nvt.edr -o temperature.xvg

#At the prompt, type "16 0"#

xmgrace temperature.xvg
 
#step 8: NPT  equilibration#

gmx grompp -f npt.mdp -c nvt.gro -r nvt.gro -t nvt.cpt -p topol.top -o npt.tpr

gmx mdrun -deffnm npt

gmx energy -f npt.edr -o pressure.xvg

#At the prompt, type "18 0"#

gmx energy -f npt.edr -o density.xvg

#At the prompt, type "24 0"#

 
#step 9: Production MDrun#

gmx grompp -f md.mdp -c npt.gro -t npt.cpt -p topol.top -o md_0_1.tpr

#step 10: Execution of MD#

either:

gmx mdrun -deffnm md_0_1

or

gmx mdrun -deffnm md_0_1 -nb gpu

