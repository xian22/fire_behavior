cmake_minimum_required( VERSION 3.14 )

# 1. Initialize ecbuild (The JEDI Build System)
find_package( ecbuild REQUIRED )

project( ufs_fire_behavior VERSION 0.0.1 LANGUAGES Fortran CXX )

# 2. Include JEDI system macros
include( ecbuild_system )

# 3. Find Dependencies the "JEDI way"
ecbuild_find_package( NAME NetCDF REQUIRED )
ecbuild_find_package( NAME MPI REQUIRED )

# ------------------------------------------------------------------------------
# Define Source Files (Adapted from your original list)
# ------------------------------------------------------------------------------

# State
list(APPEND _state_files state/state_mod.F90
             state/ignition_line_mod.F90
             state/tiles_mod.F90)

list(APPEND _share_files share/proj_lc_mod.F90
             share/datetime_mod.F90
             share/fuel_mod.F90
             share/ros_mod.F90
             share/fmc_mod.F90
             share/constants_mod.F90)

# IO
list(APPEND _io_files io/namelist_mod.F90
             io/geogrid_mod.F90
             io/wrf_mod.F90
             io/netcdf_mod.F90
             io/stderrout_mod.F90)


# WRF Physics
list(APPEND _wrffire_physics physics/level_set_mod.F90
             physics/fire_driver_mod.F90
             physics/fire_model_mod.F90
             physics/fire_physics_mod.F90
             physics/fmc_wrffire_mod.F90
             physics/ros_wrffire_mod.F90
             physics/fuel_anderson_mod.F90)

# Driver
list(APPEND _driver_files driver/advance_mod.F90
             driver/initialize_mod.F90)

# ------------------------------------------------------------------------------
# Build the Library
# ------------------------------------------------------------------------------
# We use ecbuild_add_library instead of add_library.
# This automatically handles exporting the target so cfbm-jedi can find it.
ecbuild_add_library( TARGET   firelib
                     SOURCES  ${_state_files} ${_share_files} ${_io_files} ${_wrffire_physics} ${_driver_files}
                     PUBLIC_LIBS NetCDF::NetCDF_Fortran ${MPI_Fortran_LIBRARIES} )

# ------------------------------------------------------------------------------
# Build the Executable (Optional, but good for testing the model standalone)
# ------------------------------------------------------------------------------
ecbuild_add_executable( TARGET fire_behavior.exe
                        SOURCES driver/fire_behavior.F90
                        LIBS firelib )

# ------------------------------------------------------------------------------
# Finalize
# ------------------------------------------------------------------------------
ecbuild_install_project( NAME ufs_fire_behavior )
