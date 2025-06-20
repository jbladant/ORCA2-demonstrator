# Tutorial for adding variables to the model output files

The variables that *could* be output are listed in `EXP00/field_def_nemo-XXX.xml`, with `-XXX` being `-oce` for the physical ocean variables, `-ice` for the ice, and `-pisces` for the biogeochemistry. 

The variables that *are* output are defined in `EXP00/file_def_nemo-XXX.xml`. 

__

## Contents of `field_def` and `file_def`

find XIOS documentation
__

## How to add a new variable 

### Output variables that exist in `field_def`


#### Example: Output the depth of the minimum oxygen across the water column 
To output the minimum oxygen depth (`ZO2MIN`) modify 

### New diagnostics
If the variable you want to output is not 

Note: Need to recompile 

___
## Tips

If there's an error, the model may run but blow up at the end. 




