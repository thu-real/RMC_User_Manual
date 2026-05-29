.. _section_eng_conti_surf:

Continuation Surface Source
=================================

RMC supports the output function for continuation surface source calculation files. The continuation surface source calculation 
is used to record particle tracks on a surface for shielding calculations. Additional information is defined by the BinaryOut module.

Continuation Surface Source File Output Module Input Options
-----------------------------------------------------------------

.. code-block:: none

    Binaryout
    WrtSurfSrc  Write=<flag>  SYMM=<symm>  PARTYPE = <parms>
                Surf = <Surface_vector_group>  CeL=<cell_vector_group>
                WCylces=<wcycles> Wssa=<flag>

Among them,

-   **Write** \  specifies whether to output continuation calculation files, 1 indicates output, 0 indicates no output, the default value is 0.

-   **Cel** \  only applies to criticality calculations. It records source particles generated in the specified cell during criticality calculations.

-   **Symm** \  specifies the method for recording particle tracks. 0 represents symmetric recording, meaning the surface described in the Surf option can only 
    be spherical, and only one can be defined, this situation is rare; and users need to determine if it can be used. 1 represents recording as 
    described in Surf option. 2 represents recording tracks crossing the surface described in the Surf option, regardless of track direction. The default value is 1.

-   **Partype** \  specifies the type of tracks to be recorded, 0 represents neutrons, 1 represents photons. By default, it includes all types of tracks used in the calculation.

-   **Surf** \  specifies the surfaces to be recorded, detailed below. The input format is Ids1(Idc1 Idc2 Idc3) Ids2, such as: Surf=-1(2 -3) 3. 
    Ids represents the index of the surface to be recorded, which can be positive or negative. The sign represents whether the cosine of the 
    angle between the particle track crossing the surface and the surface normal is positive or negative. The parentheses represent the cell index, 
    which can be positive or negative. "+" (default) indicates entering the cell, "-" indicates leaving the cell. For example, Surf=-1(2 -3) 3 indicates 
    recording tracks crossing surface 1 with a negative angle to the normal direction of surface 1, entering cell 2 or leaving cell 3, or tracks 
    along the positive angle to the normal direction of surface 3. If Symm is 2, it records tracks crossing surface 1 and entering cell 2 or 
    leaving cell 3, or crossing surface 3.

-   **Wcycles** \  specifies the active generations to be recorded in continuation calculations. If not defined, all active generations are recorded by default.

-   **MCNP5_Wssa** \ =1 indicates converting the continuation surface source file output by RMC to MCNP5-1.20 and MCNP5-1.51 format surface source files.
    **MCNP5_Wssa** \  default value is 0, and no conversion is performed when it is 0. 
    **MCNP6_Wssa** \ =1 means converting the continuation surface source file output by RMC to MCNP6.1.1 format surface source files. 
    **MCNP6_Wssa** \  default value is 0, and no conversion is performed when it is 0. This function requires using RMC's python framework for calculation. 
    Create a test folder, copy runner.py from RMC's python framework, create a workspace folder, place the input card, RMC executable file, and xsdir in it, 
    then call runner.py from outside the folder. The command to run is python3 runner.py workspace/inp --mpi number_of_mpi_cores. The output MCNP format surface 
    source files will be in the archive folder, with separate outputs for MCNP5 and MCNP6 formats. The MCNP6 format surface source file is named rssa_mcnp6.1.1, 
    and the MCNP5 format surface source files are named rssa_mcnp5-1.20 and rssa_mcnp5-1.51.

Continuation Surface Source File Usage
-------------------------------------------

.. code-block:: none

     SurfSrcRead  OldSurf=< S1 S2 S3……Sn >  NewSurf = < S11 S21 S31…Sm1…Smn >
                  Cell=<cell_vector_group>  Partype=<params>   Coli=<coli>  Wtm=<wtm>
                  Axis=<x1 x2 x3>   Extent=<id>  Posace=<posace>  Tr=<id> ssr=<flag>


where,

-  **OldSurf** \ specifies the surfaces in the original calculation to use the particle tracks that pass through
   these surfaces. The default is to use all surfaces in the original calculation.
-  **NewSurf** \ specifies the surface that the original surface corresponds to in the new calculation. One surface
   can correspond to multiple surfaces, that is, the number of surfaces in the input card must be an integer
   multiple of the number of surfaces in the \ **OldSurf** \ input card. When it is not specified, the surface in
   **oldsurf** \ is used by default ; if a surface corresponds to multiple surfaces, the corresponding
   relationship must be defined in TR, and the two surfaces should be the same size.
-  **Cell** \ specifies the cells in the original calculation to indicate the use of the paths in these cells.
-  **Partype** \ specifies the type of track particle to use. A value of 0 indicates neutrons and a value of 1 indicates
   photons. The default is to include the track type contained in the continuation surface source file.
-  **Coli** \ is the collision type of the specified track. A value of -1 means only tracks that have not experienced
   collision are selected, a value of 0 means any track is recorded, and a value of 1 means only tracks that have
   experienced collision are recorded. The default value is 0;
-  **Wtm** \ specifies the multiplier by which the weight of the selected track is multiplied.
-  **Axis，Extent，Posace** \ can only be used when the corresponding calculation \ **SYMM** \ = 0 is output in the
   subsequent calculation file. \ **Axis** \ represents the defined axis, and \ **Extent** \ is the bias operation for the
   particles along the specified axis, which is specifically defined in the \ **Distribution** \ input card; with two
   different forms of definition:
   1. \ **Axis** \ = u v w;
   2. \ **Axis** \ = D+ positive integer, indicating that \ **Axis** \ is described by the distribution whose ID in the
   \ **Distribution** \ is the positive integer.
   \ **Posace** \ selects particles within the specified cosine of the axis corresponding to \ **Axis** \.
-  **Tr** \ specifies the surface-to-surface correspondence. When the input is positive, it indicates the corresponding
   correspondence, that is, the spatial transformation relationship. If it is a D+ positive integer, it indicates
   the corresponding bias operation: when it is defined as D+ positive integer, the value of the positive
   integer indicates the user index number defined in the corresponding \ **Distribution** \ input option. It only
   supports the distribution of discrete values, and then performs the corresponding offset operation according
   to the discrete value definition in Type = 1 in the corresponding \ **Distribution** \ input option.
-  When the number of particles used in the calculation of the fixed source of the connecting surface source is
   greater than the number of particles in the connecting surface source file, sampling will be repeated until
   the number of particles is equal to the number of particles used in the calculation of the fixed source;
   otherwise, sampling will not be repeated.
-  **ssr** \ specifies whether to read in the MCNP format surface source file. When it is equal to 1,
   it means that the MCNP format surface source file is read in. Currently, MCNP6.1.1, MCNP5-1.20 and MCNP5-1.51
   format surface source files can be read; the default is 0, which means that the RMC format surface source file is
   read in. At the same time, this function needs to use RMC's python framework for calculation. Create a new test
   folder (users can name the folder by themselves), copy the runner.py in the RMC python framework to the newly created
   test folder, and create a new folder workspace (similarly, users can name the folder by themselves, the default is
   workspace), copy the input card, RMC executable file, xsdir and MCNP output surface source file wssa (if not wssa,
   rename it wssa) to the workspace folder, and then call runner.py outside the folder to run normally. The running
   command on the Linux server is python3 runner.py. If you want to calculate on a supercomputer, the calculation command
   refers to the python call RMC running part in: :ref:`section_eng_run_exe`.

-  If the user does not want to copy the RMC python framework to the calculation folder when the ssr option is turned on,
   you can use pyinstaller to package runner.py into an executable file, and then use the executable file for
   calculation. The calculation command remains unchanged. On the Linux server, the command is ./runner workspace/inp
   --mpi mpi core number (replace python3 runner.py with ./runner). If you want to calculate on a supercomputer, replace
   python3 runner.py with ./runner in the calculation command.

-  At present, RMC can support the use of surface source files in MCNP format in addition to the surface source files
   output by itself. This module is specifically defined in the external source module. For details, please refer to the
   source description module input card in :ref:`section_eng_external_source` for calculation. When using the surface source
   file WSurfSrc output by the RMC program, the WSurfSrc file has to be renamed to SurfSrc; when using the surface source
   file in MCNP format, you need to turn on the ssr option in the SurfSrcRead tab.


Continuation Surface Source Module Example
-------------------------------------------
-  Output example of the Continuous Surface Source file

.. code-block:: c

    BinaryOut
    SurfSrcWrt    surf = 1(-2 3) cel =2 symm=0 Partype = 0 1

This example is a continuation of the surface source output module, where the track that passes through
surface 1 and has a positive angle with the positive normal direction of surface 1, and leaves cell 2 or enters cell 3 is
recorded. At the same time, the source particles generated in cell 2 during the critical calculation are recorded. The
track recording method is symmetrical recording, and the recorded particle track types are neutrons and photons.

-  Continuous Surface Source Example

.. code-block:: c

    SurfSrcRead  OldSurf=1 2 NewSurf = 1 2
    Cell= 3 Partype=0 Coli=1 Wtm=1.0


This example uses the neutron tracks on the original surface number 1 and 2 respectively, and the neutron tracks in cell 3.
The weight multiplier is 1, and these are post-collision tracks.

.. code-block:: c

    SurfSrcRead  OldSurf=3 4 NewSurf = 3 4
    Partype=0 1 Coli=1 Wtm=1.0 ssr=1


This example uses the neutron and photon tracks on the original surfaces numbered 3 and 4 respectively, with a weight multiplier
of 1, and uses the surface source file in MCNP format for subsequent calculations.

