
WRF Thompson MP scheme initialization optimization

This could be applied to WRF Thompson MP scheme which will be useed when mp_physics=8 in &physics section of namelist.input file.

Since WRF version 3.6.1, arrays of 'freezeH2O' routine has changed from 3d/2d array to 4d array.

Every MPI ranks calculates the same arrays using the same formulas. So time consumed in 'freezeH2O' subroutine does not decrease with the increased number of MPI processes. And this makes long initilization time in some cases.
And even more the main loop of 'freezeH2O' routine has loop dependencies and premature EXIT so it cannot be vectorized.

If you measure time on this routine, it will take ~200 seconds on Intel(r) Xeon(r) Phi(tm) processors and ~50 seconds on Intel(r) Xeon(r) processors. This is a huge bottleneck in WRF initialization time.

By distributing workload across MPI, this time could be reduced with the increased number of MPI. And with large number MPIs, its time will be less than 1 second so its time could be neglected.

You can get it work using the applied patch. For each WRF version you are using:

    $ cd /path/to/WRF/phys
    $ patch < mp_thompson_{WRF_VERSION}.patch


