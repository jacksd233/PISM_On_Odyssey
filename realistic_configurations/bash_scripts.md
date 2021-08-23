
## bash_scripts for submissing jobs (For Odyssey)

```
#!/bin/bash
### Job Name
#SBATCH --job-name=LIS_Run_A13_20km_190c_intel_T5Less_SMB700_top100_ELAgradual_from700to500m_in100kyr_bht0.01_100000to200000y
#SBATCH -t 480:00:00
#SBATCH --mem-per-cpu=4000
#SBATCH -p huce_intel
### Merge output and error files
#SBATCH --open-mode=append
#SBATCH -o %x.%j
### Select  nodes with 36 CPUs each for a total of 360 MPI processes
#SBATCH -n 190
### Send email on abort, begin and end
#SBATCH --mail-type=ALL
### Specify mail recipient
#SBATCH --mail-user=(email_address delete bracket) 



### Run the executable


InputFile=`ls -vr snapshots_*.000.nc | head -n1`
StartTime=`sed 's/snapshots_\(.*\)\.000\.nc/\1/' <<< "$InputFile"`

#if [ -n "$InputFile" ] ; then
#    mkdir ./${StartTime}yto800000y
#    cd ./${StartTime}yto800000y
#else
if [ -z "$InputFile" ] ; then
  InputFile=PISM_Out_20km_100000y_Gowan_SSTa5Less_thickness_300m_SMBmax600_top100_Lapse9.8_bht0.01.nc
  StartTime=100000
fi
OutName="LIS_20km.nc"
ModelTime=200000
SaveFileName=Snapshots


mpiexec -n $SLURM_NTASKS ./pismr -i $InputFile  \
                   -log_summary -options_left \
                   -Mx 451 -My 351 -Mz 101 -Mbz 11 -z_spacing equal -Lz 8000 -Lbz 4600 \
                   -sia_e 3.0 -stress_balance ssa+sia -yield_stress mohr_coulomb \
                   -tauc_slippery_grounding_lines \
                   -hydrology_tillwat_max 2 -hydrology routing \
                   -surface elevation,anomaly -climatic_mass_balance -4,0.666666667,0,700,1000 -ice_surface_temp 0,-78.4,0,8000  \
                   -surface_anomaly_file $InputFile   \
                   -skip -skip_max 10 \
                   -ys $StartTime -ye $ModelTime\
                   -bed_def lc \
                   -calving thickness_calving \
                   -thickness_calving_threshold 300 \
                   -ts_file ts_${StartTime}yto${ModelTime}y_$OutName -ts_times $StartTime:1:$ModelTime \
                   -extra_file ex_${StartTime}yto${ModelTime}y_$OutName -extra_times $StartTime:100:$ModelTime \
                   -extra_vars diffusivity,temppabase,tempicethk_basal,bmelt,tillwat,velsurf_mag,mask,thk,topg,usurf,hardav,velbase_mag,tauc,climatic_mass_balance,ice_surface_temp,taud_mag,taub_mag,taub,taud,bwat,tendency_of_ice_amount_due_to_discharge,tendency_of_ice_mass_due_to_flow,tendency_of_ice_mass_due_to_discharge \
                   -save_file snapshots -save_times 110000,120000,130000,140000,150000,160000,170000,180000,190000,200000 -save_split -save_size big \
                   -o PISM_Out_20km_200000y_Gowan_SSTa5Less_thickness_300m_SMBmax600_top100_ELAgradual_700to500m_in100kyr_Lapse9.8_bht0.01.nc -o_size big



```

## bash_scripts for Cheyenne
```
#!/bin/bash
### Job Name
#PBS -N LIS_Run_A1_20km_216c_T5Less_SMBmax700_top150_slippery_bht0.01_100000y
#PBS -A UHARXXXX (job ID)
#PBS -l walltime=12:00:00
#PBS -q regular
### Merge output and error files
#PBS -j oe
### Select  nodes with 36 CPUs each for a total of 360 MPI processes
#PBS -l select=6:ncpus=36:mpiprocs=36
### Send email on abort, begin and end
#PBS -m abe
### Specify mail recipient
#PBS -M (email_address delete bracket)



### Run the executable


InputFile=`ls -vr snapshots_*.000.nc | head -n1`
StartTime=`sed 's/snapshots_\(.*\)\.000\.nc/\1/' <<< "$InputFile"`

if [ -z "$InputFile" ] ; then

 InputFile=CS_proj4_Gowan_T_5less_bht0.01_phi10to30_ts0_dx20000.nc
 StartTime=0

fi

OutName="LIS_20km.nc"
ModelTime=100000
SaveFileName=Snapshots


mpiexec -n 216 ./pismr -i $InputFile  -bootstrap\
                   -log_summary -options_left \
                   -Mx 451 -My 351 -Mz 101 -Mbz 11 -z_spacing equal -Lz 8000 -Lbz 4600 \
                   -sia_e 3.0 -stress_balance ssa+sia -yield_stress mohr_coulomb \
                   -tauc_slippery_grounding_lines \
                   -hydrology_tillwat_max 2 -hydrology routing \
                   -surface elevation,anomaly -climatic_mass_balance -4,0.777777777778,0,700,1000 -ice_surface_temp 0,-78.4,0,8000  \
                   -surface_anomaly_file $InputFile   \
                   -skip -skip_max 10 \
                   -ys $StartTime -ye $ModelTime\
                   -bed_def lc \
                   -calving thickness_calving \
                   -thickness_calving_threshold 300 \
                   -ts_file ts_${StartTime}yto${ModelTime}y_$OutName -ts_times $StartTime:1:$ModelTime \
                   -extra_file ex_${StartTime}yto${ModelTime}y_$OutName -extra_times $StartTime:100:$ModelTime \
                   -extra_vars temppabase,tempicethk_basal,bmelt,tillwat,velsurf_mag,mask,thk,topg,usurf,hardav,velbase_mag,tauc,climatic_mass_balance,ice_surface_temp,taud_mag,taub_mag,bwat,tendency_of_ice_amount_due_to_discharge,tendency_of_ice_mass_due_to_flow,tendency_of_ice_mass_due_to_discharge \
                   -save_file snapshots -save_times 50000,60000,70000,80000,90000,95000 -save_split -save_size big \
                   -o PISM_Out_20km_100000y_Gowan_SSTa5Less_slippery_SMBmax700_top150_Lapse9.8_bht0.01.nc -o_size big


```
