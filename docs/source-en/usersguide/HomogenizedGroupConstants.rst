.. _section_eng_group_contants:

Homogenized Group Constants (Enterprise Version Only)
========================================================

RMC provides a group constant generation feature for performing homogenization calculations on assemblies or lattice cells, 
generating few-group homogenized constants for reactor core codes.

Currently, the RMC homogenization functionality includes the few-group constant generation module, region merging module, 
B1 correction module, SPH equivalence correction module, and core coupling interface module. On top of stochastic media transport, 
it also supports the generation of few-group homogenized constants for stochastic geometries.

Group Constant Module Input Card
----------------------------------

.. code-block:: none

    GroupConstant
    Universe =<UnivIndexU>
    Energy=<ErgBin>
    Wims=<Params>
    Bone=<Params>
    Equivalence=<EquivalenceMethod> <Params>
    Hybrid=<Params>
    Angular=<Params>
    Volume=<Params>


Where:

-  **GroupConstant** \  is the keyword for the homogenized group constant input card.

-  **Universe** \  specifies the spatial ID of the homogenization target, consistent with the space ID in CSG geometry.

-  **Energy** \  defines the energy group structure for the few-group constants. The default structure is a two-group system divided at 0.625 eV. 
   Users can also input specific energy values to create custom group boundaries, which should be provided in ascending order.

-  **Wims** \  indicates whether to use the two-step method for homogenization calculations. The default is enabled (Wims=1), while Wims=0 means 
   a single-step method is used. The default fine-group structure follows the 69-group WIMS energy structure.

-  **Bone** \  specifies whether to apply B1 correction. Bone=1 enables it, while Bone=0 disables it. B1 correction is used for single assembly 
   reflective models, and it is enabled by default.

-  **Equivalence** \  defines whether to perform equivalence correction. Equivalence=0 disables it, and Equivalence=1 computes discontinuity
   factors for assemblies (with 1 for quadrilateral assemblies and 2 for hexagonal assemblies, though only quadrilateral assemblies are supported).
   Equivalence=2 performs super-homogenization (SPH), requiring the number of SPH iterations to be specified in <Paras>. 
   Note: **SPH calculations are invalid for single regions, as the SPH factor is 1 for such cases. The program supports inputting a single region**
   **containing a lattice structure using the Universe card. When SPH is enabled, the program automatically searches for the highest-level lattice**
   **within the region and expands it, calculating group constants separately for each cell. This expanded region uses a single-step method and does**
   **not apply B1 correction.**
   **If the Universe card inputs multiple regions, the SPH will not expand these regions.**
   **The SPH functionality also generates an output file named <input_filename_SPH.h5>, which contains the flux and volume for each region.**
   **Input and output files can refer to the examples in the test directory under TwoFuelRod_SPH and Assembly_SPH.**

-  **Hybrid** \  specifies whether to generate few-group constants for various core codes. Currently, only \ **Hybrid=0** \  (default, no output) and 
   \ **Hybrid=1** \  (generates few-group constants required for multi-group Monte Carlo core codes in ACE format) are supported.
   ** Note: If the region is an extended homogenized subregion with SPH enabled, the output file will follow the format: <xs_user_space_ID_lattice_ID> **
   ** for extended regions, and <xs_user_space_ID> for non-extended regions. **

-  **Angular** \   is related to Hybrid=1, specifying angular information. It supports up to 5th-order angular data for multi-group Monte Carlo 
   simulations. Options are: 0, 1, 3, 5.

-  **Volume** \  defines the volume of each component. If discontinuity factors are enabled, the volume parameter will be used for component volume 
   calculations. If not specified, the system will automatically compute the volume. Note: \ **For 2D component calculations, the volume represents**
   **the component’s cross-sectional area. This card can be defined multiple times, but the number of inputs must match the Universe card.**


Group Constant Module Example
-------------------------------

PWR Fuel Rod Homogenization Calculation
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

:numref:`pwrcell_homo_eng` This is a PWR fuel rod, demonstrating the basic functionality of homogenization calculations for the PWR fuel rod.

In this example, a 2-group constant for Universe=0 is calculated and output to a file. Energy=0.625E-06 sets the energy boundary at 0.625 eV 
for the two-group calculation. Wims=0 means a direct single-step calculation is performed for the two-group constants, and Bone=0 indicates 
that the B1 correction is not applied.

|

.. code-block:: c
  :caption: Example of Homogenization Calculation for a PWR Fuel Rod
  :name: pwrcell_homo_eng

    ///////--- Pin definition : ////
    Universe 0
    cell 1      -1            mat=1  Tmp=600
    cell 3      1&3&-4&5&-6   mat=3  Tmp=600
    cell 4      -3:4:-5:6     mat=0 void=1  Tmp=600


    Surface
    surf 1 cz  0.412
    surf 3 px  -0.665    bc=1
    surf 4 px  0.665     bc=1
    surf 5 py  -0.665    bc=1
    surf 6 py  0.665     bc=1
    surf 7 px  -21.42     bc=1
    surf 8 px  21.42      bc=1
    surf 9 py  -21.42     bc=1
    surf 10 py 21.42      bc=1


    Material
    // --- Fuel (composition given in atomic densities):
    mat 1 -10.045
          92235.60c   6.89220E+20
          92238.60c   2.17103E+22
          8016.60c    4.48178E+22
    //  --- Water (composition given in atomic densities):
    mat 3  -0.7569   // moder lwtr 1001
           1001.60c   5.06153E+22
           8016.60c   2.53076E+22
    //sab 3  lwtr.01t


    Criticality
    PowerIter Keff0=1.0 Population = 100 10 30
    InitSrc Point = 0 0 0


    GroupConstant
    Universe = 0
    Energy =0.625E-06   //2-group
    WIMS=0
    BONE=0


    Plot  ColorScheme=335   Continue-calculation=1
    PlotID 2  Type = Slice   Color = Cell  Pixels= 1800 1800  Vertexes= -1 1 0  1 -1 0


|

Stochastic Geometry Homogenization Calculation
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

This example features a 25x25 stochastic geometry assembly, where each lattice cell consists of a stochastic medium block, with a packing 
fraction of PF=5.068%.

The calculation generates the 2-group homogenized cross-sections for Universe=0, with an energy boundary set at 4.0 eV. Wims=1 
indicates that a two-step framework is used to compute the few-group constants, with the fine-group structure based on the 69-group WIMS energy 
structure. Bone=0 specifies that the B1 correction is disabled, and Hybrid=1 means that a multigroup Monte Carlo core calculation 
cross-section file is generated, named xs1. Angular=1 enables the calculation of 1st-order angular data to characterize anisotropy 
for the multigroup Monte Carlo core. Equivalence=0 disables the equivalence homogenization correction for the 2-group constants.

|

.. code-block:: c
  :caption: Stochastic Geometry Assembly Homogenization Calculation Example
  :name: random_homo_eng

    ///////////// Array15 /////////////
    Universe 0
    cell 3 9&-10&11&-12&21&-22&17&-18  fill=1                          // Inside the Assembly
    cell 4 -9:10:-11:12:-21:22:-17:18  mat=0  void=1

    Universe 1 move=-32.13 -18.55 0 lat=2 pitch=1.7850 1.7850 scope=25 25 sita=60 fill=
         6 6 6 6 6 6 6 6 6 6 6 6 6 6 6 6 6 6 6 6 6 6 6 6 6
         6 6 6 6 6 6 6 6 6 6 6 6 6 6 6 6 6 6 6 6 6 6 6 6 6
         6 6 6 6 6 6 6 6 6 6 6 6 6 6 6 6 6 6 6 6 6 6 6 6 6
         6 6 6 6 6 6 6 6 6 6 6 6 6 6 6 6 6 6 6 6 6 6 6 6 6
         6 6 6 6 6 6 6 6 6 6 6 6 6 6 6 6 6 6 6 6 6 6 6 6 6
         6 6 6 6 6 6 6 6 6 6 6 6 2 2 2 2 2 2 2 2 6 6 6 6 6
         6 6 6 6 6 6 6 6 6 6 6 2 2 2 2 2 2 2 2 2 6 6 6 6 6
         6 6 6 6 6 6 6 6 6 6 2 2 2 2 2 2 2 2 2 2 6 6 6 6 6
         6 6 6 6 6 6 6 6 6 2 2 2 3 2 2 2 3 2 2 2 6 6 6 6 6
         6 6 6 6 6 6 6 6 2 2 2 2 2 2 2 2 2 2 2 2 6 6 6 6 6
         6 6 6 6 6 6 6 2 2 3 2 2 2 2 2 2 2 3 2 2 6 6 6 6 6
         6 6 6 6 6 6 2 2 2 2 2 2 2 2 2 2 2 2 2 2 6 6 6 6 6
         6 6 6 6 6 2 2 2 2 2 2 2 3 2 2 2 2 2 2 2 6 6 6 6 6
         6 6 6 6 6 2 2 2 2 2 2 2 2 2 2 2 2 2 2 6 6 6 6 6 6
         6 6 6 6 6 2 2 3 2 2 2 2 2 2 2 3 2 2 6 6 6 6 6 6 6
         6 6 6 6 6 2 2 2 2 2 2 2 2 2 2 2 2 6 6 6 6 6 6 6 6
         6 6 6 6 6 2 2 2 3 2 2 2 3 2 2 2 6 6 6 6 6 6 6 6 6
         6 6 6 6 6 2 2 2 2 2 2 2 2 2 2 6 6 6 6 6 6 6 6 6 6
         6 6 6 6 6 2 2 2 2 2 2 2 2 2 6 6 6 6 6 6 6 6 6 6 6
         6 6 6 6 6 2 2 2 2 2 2 2 2 6 6 6 6 6 6 6 6 6 6 6 6
         6 6 6 6 6 6 6 6 6 6 6 6 6 6 6 6 6 6 6 6 6 6 6 6 6
         6 6 6 6 6 6 6 6 6 6 6 6 6 6 6 6 6 6 6 6 6 6 6 6 6
         6 6 6 6 6 6 6 6 6 6 6 6 6 6 6 6 6 6 6 6 6 6 6 6 6
         6 6 6 6 6 6 6 6 6 6 6 6 6 6 6 6 6 6 6 6 6 6 6 6 6
         6 6 6 6 6 6 6 6 6 6 6 6 6 6 6 6 6 6 6 6 6 6 6 6 6

    Universe 3   // Guide Tubes
    cell 24 -33&17&-18          mat=4        //water
    cell 25 33&-34&17&-18       mat=3        // Cladding
    cell 26 34&17&-18           mat=4

    Universe 6
    cell 31 -35&17&-18  mat=4
    cell 32  35&17&-18  mat=4



    Universe 2                        //Fuel Rods
    cell 21 -30&17&-18    fill=10
    cell 22 30&-31&17&-18  mat=2        // Helium Fill
    cell 27 31&-32&17&-18  mat=3        // Cladding FeCrAl
    cell 23 32&17&-18      mat=4



    // TRISO Particles distribution using explicit model
    Universe 10   lat = 4  MATRIC = 7 move = -0.6461 -0.6461 -176.5
                        // PARTICLE = 12
                        // PF = 0.35
                        // RAD = 0.061
                         RSA = 0
                         TYPE = 2
                         SIZE = 0.6461 353
                        // BURNMESH=1 1 1


    Universe 7
    cell 66 -49    mat = 7

    Universe 12
    cell 60 -44 mat=1        tmp=900
    cell 61 44&-45 mat= 5    tmp=900
    cell 62 45&-46 mat= 6    tmp=900
    cell 63 46&-47 mat= 7    tmp=900
    cell 64 47&-48 mat= 8    tmp=900
    cell 65 48 mat=7         tmp=900



    GroupConstant
    Universe = 0
    Energy =4.0E-06
    WIMS=1    // two stage homogenization
    BONE=0    // basing on two-stage and using critical spectrum to rehomogenize the fine group XS
    Hybrid=1   // Output xsout for mcnp_mg
    Angular=1  // 1 for the default, as p1
    Equivalence=0   //2 for SPH,  1 for DF  and 3 for SPE




    Surface
    surf 30 cz 0.6461
    surf 31 cz 0.6546
    surf 32 cz 0.71158
    surf 33 cz 0.8043
    surf 34 cz 0.8613
    surf 35 cz 0.8925             // water reflector
    surf 9  px  -13.4  bc=1
    surf 10 px   13.4  bc=1
    surf 11 p  -.5773502692 -1 0 -15.4536   bc=1
    surf 12 p  -.5773502692 -1 0  15.4536   bc=1
    surf 21 p   .5773502692 -1 0 -15.4536   bc=1
    surf 22 p   .5773502692 -1 0  15.4536   bc=1
    surf 13 px  -13.4  bc=1
    surf 14 px   13.4  bc=1
    surf 15 p  -.5773502692 -1 0 -15.4536  bc=1
    surf 16 p  -.5773502692 -1 0  15.4536  bc=1
    surf 19 p   .5773502692 -1 0 -15.4536  bc=1
    surf 20 p   .5773502692 -1 0  15.4536  bc=1
    surf 17 pz -176.5 bc=1
    surf 18 pz  176.5 bc=1
    surf 44 so  0.0450
    surf 45 so  0.0525
    surf 46 so  0.0555
    surf 47 so  0.0590
    surf 48 so  0.0610
    surf 49 inf
    surf 50 cz  16  bc=1
    surf 61 py  -16
    surf 62 py  16
    surf 63 px  -16
    surf 64 px  16


    Material
    mat 1 -12.95                       //UC
      92235.30c 16.10097657
      92238.30c 83.89902343
    6000.30c  100
    mat 2 -0.0022                    // Helium
      2004.30c  1.0
    mat 3  1.7767103E-02            //FeCrAl
      26054.30c  7.99520E-04
      26056.30c  1.22593E-02
      26057.30c  2.66507E-04
      24052.30c  3.55342E-03
      13027.30c  8.88356E-04
    mat 4 -0.74    // Water
      8016.30c  1.0
      1001.30c  2.0
    //sab 4  lwtr.62t
    mat 5 -1.05
      6000.30c    1.0
    //sab 3 grph.65t
    mat 6 -1.9
      6000.30c    1.0
    //sab 4 grph.65t
    mat 7 9.55236E-02                   //SiC
      6000.30c     4.77618E-02
    // 14000.30c     4.77618E-02
    //sab 5 grph.65t
    mat 8 -1.1
      6000.30c    1.0
    //sab 4 grph.65t


    Criticality
    PowerIter keff0=1.0 population = 20 10 20
    InitSrc point=0 0 0

    PLOT  ColorScheme=3     Continue-calculation=1
    PlotID 1  Type = slice   Color = Mat  Pixels=9000 9000   Vertexes=-16 16 0  16 -16 0
    PlotID 2  Type = Slice   Color = Cell  Pixels= 1800 1800  Vertexes= -1 22 0  22 -1 0


|


PWR Assembly Discontinuity Factor Calculation
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

This example involves a 17x17 Pressurized Water Reactor (PWR) geometry assembly.

The calculation computes the 2-group homogenized cross-sections for Universe=0, using 4.0 eV as the energy boundary. The two-step method 
is employed (Wims=1) with the 69-group WIMS energy structure. The B1 correction (Bone=0) is disabled for this calculation. The output 
includes a multigroup cross-section file required for Monte Carlo core calculation (Hybrid=1), named xs1, with Angular=1 enabling first-order 
angular data for anisotropy representation. Equivalence=1 is activated to compute the boundary discontinuity factors and angular discontinuity 
factors for the assembly, where the assembly type is defined as rectangular.

|

.. code-block:: c
  :caption: PWR Assembly Discontinuity Factor Calculation Example
  :name: pwr_df_eng

    ////////  PWR assembly ////////
    UNIVERSE 0
    CELL 1   -6 : 7 : -8 : 9   mat = 0   void = 1               // Assembly outside
    CELL 2   6 & -7 & 8 & -9   mat = 0   Fill = 8               // Assembly inside


    UNIVERSE 8  lat = 1  pitch = 1.26 1.26 1    scope = 17  17  1  fill =
      1 1 1 1 1 1 1 1 1 1 1 1 1 1 1 1 1
      1 1 1 1 1 1 1 1 1 1 1 1 1 1 1 1 1
      1 1 1 1 1 3 1 1 3 1 1 3 1 1 1 1 1
      1 1 1 3 1 1 1 1 1 1 1 1 1 3 1 1 1
      1 1 1 1 1 1 1 1 1 1 1 1 1 1 1 1 1
      1 1 3 1 1 3 1 1 3 1 1 3 1 1 3 1 1
      1 1 1 1 1 1 1 1 1 1 1 1 1 1 1 1 1
      1 1 1 1 1 1 1 1 1 1 1 1 1 1 1 1 1
      1 1 3 1 1 3 1 1 3 1 1 3 1 1 3 1 1
      1 1 1 1 1 1 1 1 1 1 1 1 1 1 1 1 1
      1 1 1 1 1 1 1 1 1 1 1 1 1 1 1 1 1
      1 1 3 1 1 3 1 1 3 1 1 3 1 1 3 1 1
      1 1 1 1 1 1 1 1 1 1 1 1 1 1 1 1 1
      1 1 1 3 1 1 1 1 1 1 1 1 1 3 1 1 1
      1 1 1 1 1 3 1 1 3 1 1 3 1 1 1 1 1
      1 1 1 1 1 1 1 1 1 1 1 1 1 1 1 1 1
      1 1 1 1 1 1 1 1 1 1 1 1 1 1 1 1 1

    UNIVERSE 1 move = 0.63 0.63 0                 // Fuel rod
    cell  3   -1       mat = 1      inner = 1     // Fuel
    cell  4   1 & -2   mat = 3      inner = 1     // Air
    cell  6   2        mat = 5                    // water

    UNIVERSE 3 move = 0.63 0.63 0                 // Guide tube
    cell  11  -5       mat = 5      inner = 1     // water
    cell  13  5        mat = 5                    // water

    SURFACE
    surf  1  cz   0.4096
    surf  2  cz   0.4178
    surf  3  cz   0.4750
    surf  4  cz   0.5690
    surf  5  cz   0.6147
    surf  6  px   0         bc = 1
    surf  7  px   21.42     bc = 1
    surf  8  py   0         bc = 1
    surf  9  py   21.42     bc = 1

    MATERIAL
    mat 1  -10.196
           92235.71c   6.9100E-03
           92238.71c   2.2062E-01
           8016.71c    4.5510E-01
    mat 3  -0.001
           8016.71c    3.76622E-5
    mat 5  9.9977E-02
           1001.71c    6.6643E-02
           8016.71c    3.3334E-02
    //sab 5  HH2O.90t

    CRITICALITY
    PowerIter   population = 2000 20 50  // keff0 = 1.0
    InitSrc point = 12 12 0


    TALLY
    SurfTally 1 type = 1  Surf = 6 area = 21.42 Energy = b1
    SurfTally 1 type = 1  Surf = 9 area = 21.42 Energy = b1

    GroupConstant
    Universe = 0
    Energy = 4E-06
    WIMS=1
    BONE=0
    Hybrid=1  // Output xsout for mcnp_mg
    Angular=1  // 1 for the default, as p1\n
    EQUIVALENCE = 1 1    ///1:DF calculation 1: RECT type assembly
    ///volume = 458.8164

