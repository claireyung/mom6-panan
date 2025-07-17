# MOM6 SIS2

Copied my input files from ACCESS-OM3 regional panan (see that file for info on how it was made)

Cloned mom6 panan COSIMA repo and made a new branch for IS dev

Now need to make exchange grid and computational mask  https://github.com/COSIMA/mom6-panan/wiki/Preparing-inputs-for-a-new-configuration 

The tools are here https://github.com/NOAA-GFDL/FRE-NCtools/tree/main 

So I git cloned above in /scratch/x77/cy8964

module load intel-compiler
module load openmpi
module load netcdf
Module load eco

autoreconf -i
./configure
make
make install

(Didn’t finish but made the stuff in src so maybe ok)

Cd src

cp /g/data/x77/cy8964/mom6/input/input-8km-SIS2/topog_Charrassin_opencavity_cropped_mindepth5.nc .
ncap2 -s 'defdim("ntiles",1)' -A topog_Charrassin_opencavity_cropped_mindepth5.nc topog_Charrassin_opencavity_cropped_mindepth5.nc (need to add dim “tile”)
cp /g/data/x77/cy8964/mom6/input/input-8km-SIS2/ocean_hgrid_cropped.nc .

./make_solo_mosaic --num_tiles 1 --dir . --mosaic_name ocean_mosaic  --tile_file ocean_hgrid_cropped.nc --periodx 360

./make_quick_mosaic --input_mosaic ocean_mosaic.nc --mosaic_name grid_spec  --ocean_topog topog_Charrassin_opencavity_cropped_mindepth5.nc 

./check_mask --grid_file ocean_mosaic.nc --ocean_topog topog_Charrassin_opencavity_cropped_mindepth5.nc   --layout 4320,1442

[cy8964@gadi-login-03 src]$ cp atmos_mosaic_tile1Xland_mosaic_tile1.nc /g/data/x77/cy8964/mom6/input/input-8km-SIS2/
[cy8964@gadi-login-03 src]$ cp atmos_mosaic_tile1Xocean_mosaic_tile1.nc /g/data/x77/cy8964/mom6/input/input-8km-SIS2/
[cy8964@gadi-login-03 src]$ cp land_mask.nc /g/data/x77/cy8964/mom6/input/input-8km-SIS2/
[cy8964@gadi-login-03 src]$ cp ocean_mask.nc /g/data/x77/cy8964/mom6/input/input-8km-SIS2/
[cy8964@gadi-login-03 src]$ cp land_mosaic_tile1Xocean_mosaic_tile1.nc /g/data/x77/cy8964/mom6/input/input-8km-SIS2/
[cy8964@gadi-login-03 src]$ cp mask_table.2454376.4320x1442 /g/data/x77/cy8964/mom6/input/input-8km-SIS2/
[cy8964@gadi-login-03 src]$ cp ocean_mosaic.nc /g/data/x77/cy8964/mom6/input/input-8km-SIS2/
 cp /scratch/x77/cy8964/FRE-NCtools/src/grid_spec.nc /g/data/x77/cy8964/mom6/input/input-8km-SIS2/

Modified config.yaml, input.nml, copied overrides and ice diagnostics, SIS_input topog file, MOM_input names and mask table etc

X,Y actually processor numbers not grid

./check_mask --grid_file ocean_mosaic.nc --ocean_topog topog_Charrassin_opencavity_cropped_mindepth5.nc   --layout 63,43 
(from access-om3 numbers)

Had to be careful about number of processors (=no. ocean cpus in access-om3)

Still crashed during first timestep before producing output.
