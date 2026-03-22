---
layout: page
title: How to use KCM with phono3py
description: How to use the KCM with phono3py output.
---

Here are given the main steps to follow to calculate the thermal conductivity in the KCM
using the output from phono3py. 

### Phono3py calculation

Download phono3py and follow the instructions given [here](https://atztogo.github.io/phono3py/index.html)
to obtain the force constants for the desired material using [VASP](https://www.vasp.at/){:target="_blank_"} or [Quantum Espresso](http://www.quantum-espresso.org/){:target="_blank_"}.

In the next commands, if you use <b>VASP</b> use `-c POSCAR`, with  <b>Quantum Espresso</b> use `--pwscf -c incar.in`

Once you have the FORCES_FC2,  FORCES_FC3, and  FORCE_SETS files execute

    phono3py --dim="2 2 2" --sym-fc -c POSCAR

This will provide the harmonic and anharmonic force constants files fc2.hdf5 and fc3.hdf5.

`--dim` corresponds to the cell dimension used to obtain the force constants.

Next, execute your adapted command of this example:

```javascript
phono3py --dim="2 2 2" --fc3 --fc2  --pa="0 1/2 1/2 1/2 0 1/2 1/2 1/2 0" 
        -c POSCAR --mesh="20 20 20" --br --nu  --ts="300 400" --isotope 
        --mv="2.01e-4 2.01e-4" -o name
```
`--fc3` and `--fc2` is used to avoid new calculation of the force constants done in the previous step.

`--pa` is used to reduce to the primitive cell. This reduce the computational time. Here you have to introduce the primitive vectors as done in the example for the FCC diamond-like structure.

`--br` is used to fast calculation of the relaxation times.

`--nu` is used to split _N_ and _U_ processes. <b> This tag is REQUIRED to run the KCM calculation </b>. 

`--ts` indicates the temperatures to run the calculations.

`--isotope` calculates the natural isotope concentration scattering.

`--mv` indicates the value of the isotope strength. If is not indicated is used natural isotope concentration. Notice that you have to indicate as many values as
atoms you have in the primitive cell (if using `--pa` option) or in the conventional cell according to the POSCAR. 

`-o` is used with an extra `name` to avoid overwirtting of the output file.

This calculation will provide a `kappa-mxxx.hdf5`, where `xxx` is the mesh indicated above.

### KCM calculation

To run the KCM calculation you need to have installed [phono3py](https://atztogo.github.io/phono3py/index.html).

Start by cloning the <b>kcm</b> folder including the `KCM.py` script and `INPUT` file by executing:

    git clone https://github.com/physta/kcm_script

Copy the `KCM.py` and `INCAR` files in the same folder where you have `kappa-mxxx.hdf5` and `POSCAR` or `incar.in` of the material you want to calculate the thermal conductivity. Find also a version KCM_py3.py written in python3 to be used with the last version of Phono3py.

Set the parameters of your calculation in the `INPUT` file according to the [INPUT](https://physta.github.io/input_file/) description.

To run the KCM calculation execute:

    python KCM.py --pa="0 1/2 1/2 1/2 0 1/2 1/2 1/2 0" POSCAR kappa-mxxx.hdf5

Use only `--pa` if it was also used in the previous phono3py calculation. Execute the <b>Si-example</b> with the previous script to test it.

By default the thermal conductivity and NL-length will be stored at K<math>&lowbar;</math>T<math>&lowbar;</math><b>size</b><math>&lowbar;</math><b>component</b><math>&lowbar;</math><b>mesh</b>.dat .

If you use <b>Quantum Espresso</b> rewrite your `incar.in` according to the `POSCAR` format, which only requires the cell vectors and atomic positions, and run the KCM as:

    python KCM.py --pa="0 1/2 1/2 1/2 0 1/2 1/2 1/2 0" POSCAR kappa-mxxx.hdf5
 


