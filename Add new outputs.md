# Tutorial for adding variables to the model output files

The variables that *could* be output are listed in `EXP00/field_def_nemo-XXX.xml`, with `-XXX` being `-oce` for the physical ocean variables, `-ice` for the ice, and `-pisces` for the biogeochemistry. 

The variables that *are* output are defined in `EXP00/file_def_nemo-XXX.xml`. 

* [Contents of `field_def` and `file_def`](#field_and_file)
* [How to add a new variable](#add_variable)
* [Tips](#tips)

## <a name="field_and_file"></a> Contents of `field_def` and `file_def`

find XIOS documentation
__

## <a name="add_variable"></a> How to add a new variable 

### Output variables that exist in `field_def`
As an example, we can add the depth of the minimum oxygen concentration across the water column. This variable can be found in `field_def_nemo-pisces.xml` under `ZO2MIN`. 
```
<field id="ZO2MIN"    long_name="Depth of oxygen minimum concentration"    unit="m"    />
```
To output it during the model run, add it to `file_def_nemo-pisces.xml`. For example, to output `ZO2MIN` with a monthly frequency, find the file group that defines this:
```
<file_group id="trc_1m" output_freq="1mo" output_level="10" enabled=".TRUE."> <!-- real monthly files -->
```
and add a line
```
<field field_ref="ZO2MIN"    name="ZO2MIN"   operation="average" freq_op="1mo" > </field>
```
If you now rerun the NEMO executable, you should find the `ZO2MIN` variable in `ORCA2_1m_00010101_00021231_ptrc_T.nc`. 


### New diagnostics
If the variable you want to output is not 

Note: Need to recompile 


## <a name="tips"></a>Tips

If there's an error, the model may run but blow up at the end. 




