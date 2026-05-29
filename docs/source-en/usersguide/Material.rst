.. _section_eng_material:

Material
==========

The material input module describes the composition of materials, including material density, the corresponding database of nuclides, 
nuclide fractions, and options for defining parameters related to continuous energy cross-sections or multi-group cross-section databases.

.. _section_eng_mat_mat:

Material Input Options under the ACE Format Database
--------------------------------------------------------

Ordinary Material Input Options can be defined as:

.. code-block:: none

  Mat <mat_id> <density>
      <zaid.xxx> <fraction>
      <zaid.xxx> <fraction>
      ……



where，

-  **Mat** \ is the keyword for the Material input option;

-  **mat_id** \ refers to the material ID，which corresponds to the filling material in the Cell input option;

-  **density** \ refers to the overall material density. \ **density > 0** \ indicates atomic density, where the units are
   10\ :sup:`24`\ atoms/cm\ :sup:`3`\; \ **density < 0** \ refers to mass density, where the units are g/cm\ :sup:`3`\; 
   \ **density = 0**\ RMC will automatically calculate the material density.

-  **zaid.xxx** \ specifies the ACE database identifier cross-section database corresponding to the nuclide, where \ **zaid** \ is the nuclide ID
   and the suffix \ **.xxx** \ specifies the type of database. For ease of distinction, it is recommended that users use the suffix \ **.xxc** \  
   for continuous energy ACE databases and the suffix \ **.xxm** \ for multi-group databases when generating the database. For the specific nuclides 
   and types corresponding to the cross-section database, please refer to the database index file xsdir;

-  **fraction** \ refers to the fraction of the nuclide within the material. If \ **fraction > 0** \, the value represents the
   fraction of atomic density (relative value), while if \ **fraction < 0** \, the value represents the fraction of mass
   density (relative value). For a single material, all values of  \ **fraction** \ must have the same plus/minus signs;

In addition to Ordinary Material Input Options, RMC also provides Thermal Material Input Options to specify the
corresponding thermalization database for the continuous energy ACE cross-section.

.. code-block:: none

  Sab <mat_id> <zaid.xxx>
               <zaid.xxt>
               ……



where,

-  **Sab** \ is the keyword for the Thermal Material Input Options;

-  **mat_id** \ is the material ID, which is identical to the material ID defined in \ **Mat** \;

-  **zaid.xxx** \ specifies the thermalization cross-section database identifier used by the nuclide. Please refer to the database index file
   xsdir for further details;

Material Input Card Based on HDF5 Format AIS Database
--------------------------------------------------------

Currently, the HDF5 format AIS database supports neutron transport calculations for critical sources and fixed sources. The supported features include:
all cross-section online processing functions, mesh, surface, and grid counters (type 1-8), cross-section counters, point counters, material 
perturbation, UFS method, source convergence diagnostics, and acceleration.

The input card for ordinary materials is:

.. code-block:: none

  Mat <mat_id> <density>
      <IsoSym> <FRACTION=...> <ENDFVERSION=...> <TMP=...>
      <IsoSym> <FRACTION=...> <ENDFVERSION=...> <TMP=...>
      ……



Where:

-  **Mat**\  is the keyword for the material input card.

-  **mat_id**\  is the material identifier, corresponding to the filling material in the Cell input card.

-  **density** \ refers to the overall material density. \ **density > 0** \ indicates atomic density, where the units are
   10\ :sup:`24`\ atoms/cm\ :sup:`3`\; \ **density < 0** \ refers to mass density, where the units are g/cm\ :sup:`3`\; 
   \ **density = 0**\ RMC will automatically calculate the material density.

-  **IsoSym**\  specifies the isotope identifier; the HDF5 format AIS nuclear database is named based on isotope identifiers.

-  **FRACTION**\ refers to the fraction of the nuclide within the material. If \ **fraction > 0** \, the value represents the
   fraction of atomic density (relative value), while if \ **fraction < 0** \, the value represents the fraction of mass
   density (relative value). For a single material, all values of  \ **fraction** \ must have the same plus/minus signs;
   
-  **ENDFVERSION**\ specifies the version of the data evaluation nuclear database upon which the HDF5 format AIS nuclear database 
   is based. The program will index nuclear data files named with \ **IsoSym**\ under the directory corresponding to that version.

-  **TMP**\  specifies the Kelvin temperature of the nuclear database used for a certain isotope. When reading nuclear data, 
   the program will read data files corresponding to temperature deviations of less than or equal to 0.1 K based on the user-input 
   temperature value.

In addition to ordinary material input cards, RMC also provides a thermalized material input card to specify the corresponding thermalization 
database for continuous energy ACE cross-sections.

.. code-block:: none

  Sab <mat_id>
        <IsoSym> <ENDFVERSION=...> <TMP=...>
        <IsoSym> <ENDFVERSION=...> <TMP=...>
        ……



Where:

-  **Sab**\ is the keyword for the thermalized material input card.

-  **mat_id**\ is the material identifier that matches that in the \ **Mat**\ card and must be followed by a newline before entering the \ **IsoSym**\ parameter.

-  **IsoSym**\ specifies the identifier for the thermalization database used for nuclides.

-  **ENDFVERSION**\  specifies the version of the data evaluation nuclear database upon which the HDF5 format AIS nuclear database is based. 
   The program will index nuclear data files named with \ **IsoSym**\ under that version's directory.

-  **TMP**\  specifies the Kelvin temperature of the nuclear database used for a certain isotope. When reading nuclear data, the program will 
   read data files corresponding to temperature deviations of less than or equal to 0.1 K based on user-input temperature values.

.. _section_eng_mat_ceace:

Continuous energy ACE cross-section related Input Options
---------------------------------------------------------

If the nuclide in the material input option uses a continuous energy ACE cross-section, you can set the relevant
parameter options for it through the following input option.

.. code-block:: none

  CeAce [ErgBinHash = <flag>] [pTable = <flag>] [OTFPTB = <flag>] [OTFSAB=<flag>] 
        [DBRC = <flag>] [TMS = <flag>] [TMSTally = <flag>] [OTFDB = <flag>]

where，

-  **CeAce** \ is the keyword for the continuous energy ACE cross section related input option.

-  **ErgBinHash** \ The tab specifies whether to use the program's built-in hash table method to accelerate search
   speed of the energy mesh. When there are many nuclides, using the acceleration method can achieve higher
   computational efficiency, but it will also consume a small amount of additional memory. If the value of
   \ **ErgBinHash = 1** \  (default value), hash table acceleration is used; if the value of \ **ErgBinHash = 0** \,
   hash table acceleration is not used.

-  **pTable** \ specifies whether to use a probability table for unresolved resonances. This option is only available
   if the ACE cross section database used by the nuclide contains the UNR module. If the value of \ **pTable = 1** \
   (default), it indicates that the probability table is used, and \ **pTable = 0** \ means the probability table is
   not used.

-  **OTFPTB** \ specifies whether to use on-the-fly probability table interpolation for unresolvable
   resonance regions. This tab is used in conjunction with \ **pTable** \, and on-the-fly probability table
   interpolation is used only when \ **pTable = 1** \. \ **OTFPTB = 1** \ indicates that on-the-fly probability table
   interpolation is being used, and if \ **OTFPTB = 0** \ (default value), on-the-fly probability table interpolation is not
   used.

-  **OTFSab** \ specifies whether to use on-the-fly interpolation of thermal scattering data for the thermal energy
   zone (currently only on-the-fly interpolation of hydrogen in light water is supported). If \ **OTFSab = 0** \
   (default value), this indicates that on-the-fly thermal interpolation is not used, and if \ **OTFSab = 1** \, this means
   that on-the-fly thermal interpolation is used. Note: When using on-the-fly thermal interpolation, the cross-section
   library must be equipped with thermal cross-section libraries at several reference temperature points.
   At the same time, the corresponding thermal material "**lwtr.** \" in the material card can use a thermal library
   of any temperature in the cross-section library (only one thermal library needs to be filled in). "**xsdir_sab** \"
   is the index file of the thermal library during thermal interpolation. The file is placed in the same folder as the
   execution program. The internal structure of "**xsdir_sab**" is as follows:

   **Datapath = //** \ This is the folder where the thermal library for interpolation is located


   **lwtr 293.6K lwtr01 //** \ **lwtr** \ is the name of the thermalized material, here it is hydrogen in water;
   "**293.6K** \"is the temperature of the thermalized nuclide, in \ **K** \ (Kelvins);
   **lwtr01** \ is the name of the thermalization library used for interpolation.

-  **DBRC** \ specifies whether to use the Doppler Broadening Rejection Correction (DBRC) algorithm. If the value of
   **DBRC = 1**, this means DBRC is being used, and if the value of **DBRC = 0** (default), this means that DBRC is not
   used. Not using the DBRC algorithm will ignore the resonant elastic scattering of heavy nuclei in the
   epithermal region, and using the DBRC algorithm will increase the calculation time. The DBRC algorithm requires
   the use of the 0K database, and the nuclides in the 0K database must be named **zaid.00c** in the xsdir file.
   For example, if the user uses **1001.71c 8016.71c 92235.71c**, then the **xsdir** file must contain the
   cross-section data of **1001.00c 8016.00c 92235.00c**, and the cross-sections of these nuclides are all at 0K.
   Note that the user does not need to specify **zaid.00c** in the input card, but only has to add the relevant
   nuclides to the index file **xsdir**.

-  **TMS** \ specifies whether to use the TMS (Target Motion Sampling) algorithm. The TMS algorithm can introduce the
   feedback effect of temperature changes on the cross section. The TMS algorithm requires the use of a nuclear
   database at 0K and the temperature of the nuclide cell. Using the TMS algorithm will increase the calculation time.
   If the value of \ **TMS = 1** \, this means that TMS is used, and \ **TMS = 0** \ (default value) means that TMS is not used.

-  **TMSTally** \ specifies whether TMS is used to calculate cross sections in Tally. When cross section information is
   used in Tally, TMSTally needs to be turned on, otherwise the Tally result will be inaccurate. When cross sections
   are not needed in Tally, turning off TMSTally does not affect the accuracy of the result. When \ **TMS = 1** \,
   \ **TMSTally** \ is turned on by default, but the user can specify \ **TMSTally = 0** \ in the input card to reduce
   calculation time. When TMS is not used, TMSTally is turned off and cannot be turned on manually.

-  **OTFDB** \ specifies whether to use the Gauss-Hermitian integration method for on-the-fly Doppler broadening. This
   method can be used when the temperature of the material filled in the cell does not match the cell temperature.
   When using this method, it is recommended to select a cross-section database around 300K. Using this method will
   increase the calculation time. If the value of \ **OTFDB = 1** \, this means that the Gauss-Hermitian integration
   method is used for on-the-fly Doppler broadening, and a value of \ **OTFDB = 0** \ (default value) means that the
   Gauss-Hermitian integration method is not used for on-the-fly Doppler broadening.

-  **EDUEG** \ specifies whether to interpolate the heat number data in the RMC_DATA/neutron_hdf5 database to
   obtain the heat number cross-section data corresponding to the energy mesh in the ACE file. This option is
   turned on by default, that is, \ **EDUEG = 1** \ (default value).
   
   .. important:: In general, the energy mesh of the thermal data does not match the energy mesh in the ACE file.
      This is caused by the difference in the NJOY program version and the basic evaluation library used when the
      two databases were created. In order to avoid cross-section calculation errors (usually manifested as infinite
      cross sections or NAN) due to energy mesh mismatch during cross-section interpolation, it is recommended
      that users do not turn off this option unless necessary. If the user has ascertained that the energy mesh
      in their ACE file matches the energy mesh in the thermal database, then the user can begin to consider
      turning off this option to reduce the calculation time of cross-section interpolation during initialization.


.. _section_eng_mat_otfdbnuc:

OTFDB Nuclide Input Card
-------------------------

If the OTFDB option is turned on in the CeAce input card, the Gauss-Hermitian integration method is used for
on-the-fly Doppler broadening for all nuclides by default. At this time, the user can use this input option to
specify which nuclides should use the Gauss-Hermitian integration method for on-the-fly Doppler broadening.

.. code-block:: none

  OTFDBNUC <zaid>
           <zaid>
           ……


where,

-  **OTFDBNUC** \ is a nuclide input option for on-the-fly Doppler broadening using the Gauss-Hermitian integration method;

-  **zaid** \ is the nuclide ID.


.. _section_eng_hdf5_mat:

Material HDF5 File Input
--------------------------

In addition to reading material information and CEACE data from text files, the RMC program also supports reading material parameters 
from HDF5 files. Users can set the HDF5 file path as follows:


.. code-block:: none

  HDF5 <path_to_hdf5>

The program will read the material and CEACE information from the specified file.

.. note:: Due to the poor readability of HDF5 files for users, reading material information from HDF5 files typically occurs in large-scale 
   nuclear thermal coupling calculations. This involves reading the material HDF5 file automatically generated by the RMC program from the 
   previous burnup step to accelerate computation.
   
An HDF5 format material file contains the following sub-data blocks:

:material_density:
  Density of all user-defined materials.

:material_id:
  Identifiers for all user-defined materials.

:material_nuclide_number:
  Number of nuclides in each user-defined material.

:material_sabnuclide_number:
   Number of thermalized nuclides in each user-defined material.

:material_nuclides_id:
  Identifiers for all nuclides in each user-defined material, such as 92235.30c, 54135.30c, etc.

:material_nuclides_density:
  Density of all nuclides in each user-defined material.

:material_sabnuclides_id:
  Identifiers for thermalized nuclides in each user-defined thermalized material, such as HinH2O.92t, etc.

.. note:: To speed up the program's reading of material information, all data is stored in a one-dimensional array.

In addition to the basic data information mentioned above, the HDF5 file also includes two positional index variables for convenience:

:material_nuclide_position:
  Position of constituent nuclides of each material in the ``material_nuclides_id`` array.

:material_sabnuclide_position:
  Position of constituent thermalized nuclides of each thermalized material in the ``material_sabnuclides_id`` array.


.. _section_eng_mat_mgace:

Multi-group ACE Cross-Section Related Input Card
------------------------------------------------

If the nuclides in the material input option use multi-group ACE cross sections, \*the user must use the following
input card to specify the relevant parameter options for the multi-group cross sections.*\

.. code-block:: none

  MgAce [ErgGrp = <grp_neu> <grp_pho>] [DelayNeuFamily = <grp>]
        [Beta = <fismat1> <grp1 value> <grp2 value> ... <grpn value>
                <fismat2> <grp1 value> <grp2 value> ... <grpn value>
                ...
                <fismatm> <grp1 value> <grp2 value> ... <grpn value>]
        [Lambda = <grp1 value> <grp2 value> ... <grpn value>]



where,

-  **MgAce** \ is the keyword for the multi-group ACE cross-section related input option;

-  **ErgGrp** \ specifies the group number of multi-group neutron and multi-group photon ACE cross sections.
   When in pure neutron transport mode, the photon group number behind can be written as 0 or omitted; when in pure
   photon transport mode, the neutron group number in front needs to be written as 0 and cannot be omitted.

The following options are commonly used for space-time dynamics calculations:

-  **DelayNeuFamily** \ specifies the number of delayed neutron groups;

-  **Beta** \ specifies the delayed neutron fraction of each group of each fission material, <fismatm>
   specifies the material number of the m-th fission material, <grpn value> specifies the delayed neutron fraction of
   the nth group of the corresponding fission material, and the number of <grpi value> should be consistent
   with the value of DelayNeuFamily.

-  **Lambda** \ specifies the neutron generation time for each group of neutrons, and <grpn value> specifies
   the neutron generation time for the nth group of neutrons.

It should be pointed out that the multi-group cross-section database is closely dependent on the actual physical
problem. Therefore, the publicly released RMC package does not provide a multi-group ACE database. Users can use
other database processing software to generate a multi-group ACE cross-section database related to the actual problem.

.. _section_eng_mat_mtlib:

Photonuclear reaction and photogenic reaction database selection input option
-----------------------------------------------------------------------------

If you need to select a database of photonuclear reaction and photogenic reaction cross sections, you can set the
relevant parameters for it through this input option. The format of the input option is:

.. code-block:: none

	MTlib
        [Plib=<flag>]
        [PNlib=<flag>]

where,

-  **MTlib** \ is the keyword for selecting the input option of the photonuclear reaction and photogenic reaction
   cross section database.

-  **Plib** \ specifies the photogenic reaction cross section database type. **Plib = 04P** \ (default value)
   specifies the mcplib04p photogenic reaction cross section database.

-  **PNlib** \ specifies the photonuclear reaction cross section database type. **PNlib = 24u** \ (default value)
   specifies the endf24u photonuclear reaction cross section database.


.. _section_eng_mat_nubar:

Input Option for Adjusting the Average Fission Neutron Number
-------------------------------------------------------------

In some cases, the average number of fission neutrons needs to be adjusted proportionally to change the proliferation
capacity of the system. For example, in quasi-static dynamics calculations, the initial state needs to be critical.
If the model itself is not critical, this input option can be used to adjust it to be critical (input the effective multiplication factor keff).

The format of the input option is:

.. code-block:: none

	nubar [factor = <factor>]

where,

-  **nubar** \ is the keyword for the Adjust Average Fission Neutron Number input option.

-  **factor** \ is the factor for adjusting the average number of fission neutrons. Note that this factor is a divisor
   and the default value is 1 (indicating no adjustment). For example, if \ **factor = 2** \, the average number of
   fission neutrons used in the transport calculation will become 1/2 of the average number of
   fission neutrons in the database.


.. _section_eng_mat_dynamicmat:

Dynamic material related input options
--------------------------------------

If the nuclides in the material input card use dynamic parameters that change over time, you can set the relevant
parameter options for them through the following input options.

.. code-block:: none

  DynamicMat <mat_id> [time =<t1 t2 ... tn>] [Matdenvalue = <v1 v2 ... vn>] [Nucdenvalue = <a1 a2 ... axn>]

where,

-  **DynamicMat** \ is the keyword for dynamic material related input cards;

-  **mat_id** \ is the material serial number, which matches the material number in the \ **Mat** \ input option;

-  **time** \ input option is used in combination with the \ **Matdenvalue** \ and \ **Nucdenvalue** \ input options, to
   describe the change rules of time, material density, and the relative proportion of each nuclide in the material.
   The number of values inputted into the \ **time** \ input option and \ **Matdenvalue** \ input option are equal, indicating
   that when the time exceeds :math:`t_{i}`, the corresponding parameter is taken as :math:`v_{i}`. The number of
   values entered in the \ **Nucdenvalue** \ input option is \ **x** \ times the number of values in the \ **time** \ input option,
   and \ **x** \ is the number of nuclides in the material.


.. _section_eng_mat_cvmt:

Input Card for Continuously Varying Material
-------------------------------------------------

When a material in the input card is defined as a continuously varying material, you can use the following input card to set the relevant parameters and options:

.. code-block:: none

  cvmt <mat_id> [polytype = <polytype>] [dimension = <dimension>] [contitype = <contitype>]
                [order = <a1 a2 a3 a4>] [coeffs = <a1 a2 ... axn>] [bound = <a1 a2 a3 a4 a5 a6 a7>]

Where:

-  **cvmt** keyword specifies the input card for defining continuously varying materials.

-  **mat_id** is used to identify the material and must match the material ID defined in the **Mat** card.

-  **polytype** parameter defines the type of function used for the continuous variation. Options include: Legendre polynomial (0), Zernike polynomial (1),
   2D Legendre polynomial (2), 3D Legendre polynomial (3), Combined Legendre-Zernike polynomial (4), Power function (5), currently limited to 1D.

-  **dimension**  parameter determines the spatial dimension of the function. It supports 1D variations in the X, Y, or Z directions (values 0, 1, and 2, respectively), 
   2D variations on a circular disc (3), 3D variations in cylindrical (7) or Cartesian spaces (8), and variations using a power function (9).

-  **contitype** parameter specifies the property that varies continuously. It can represent density variation (0) or temperature variation (1).

-  **order** parameter defines the order of the variation function. For Legendre polynomials, the first three numbers specify the orders for each spatial dimension. 
   For Zernike polynomials, the fourth number specifies the polynomial order. If a particular order is not used, the value -1 should be provided as a placeholder.

-  **coeffs** parameter lists the coefficients for the terms in the variation function. For Legendre and Zernike polynomials, these coefficients must include normalization 
   factors. The values should be provided as **an*Pn**, where **an** includes the normalization constant.

-  **bound** parameter specifies the spatial boundaries for the continuously varying material. For Legendre and Zernike polynomials, the particle positions are normalized, 
   so the input must reflect the true physical dimensions of the material region. In Cartesian coordinates, the boundaries should be specified as x_min x_max y_min y_max z_min z_max. 
   For cylindrical geometry, the boundaries should be defined as x_point y_point z_min z_max r_max, where x_point and y_point represent the coordinates of the cylinder's center.

.. _section_eng_mat_example:

Material module input example
--------------------------------

Materials Module Using the Continuous Energy ACE Database
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

In the material module below, two materials UO\ :sub:`2`\ and H\ :sub:`2`\ O are first defined using the \ **Mat** \ input
option. The mass density of UO\ :sub:`2`\ is -10.196 g/cm\ :sup:`3`\ , and the atomic ratio of U235、U238 and O16
is 0.03 : 0.97 : 2.0 respectively. The atomic density of H\ :sub:`2`\ O is 0.9997 bar\ :sup:`-1`\ cm\ :sup:`-1`\ ,
and the atomic ratio of H1 and O16 is 2 : 1 respectively. A thermalization database (lwtr.60t) is specified for H1
(1001.30c) in H\ :sub:`2`\ O using the \ **Sab** \ input option. In the \ **CeAce** \ input option, \ **pTable = 0** \ indicates
that a probability table is not used, \ **ErgBinHash = 1** \ means that a hash table to accelerate energy lookup is used,
\ **DBRC = 0** \ means the DBRC algorithm is not used, \ **TMS = 0** \ means the TMS algorithm is not used, and \ **OTFDB = 1** \
indicates that the Gauss-Hermitian integral method for on-the-fly Doppler broadening is used.

.. code-block:: c

    MATERIAL
    mat 1 -10.196
        92235.30c 0.03
        92238.30c 0.97
        8016.30c 2.0
    mat 2 0.9997
        1001.30c 2.0
        8016.30c 1.0
    Sab 2 lwtr.60t
    CEACE pTable = 0 ErgBinHash = 1 DBRC = 0 TMS = 0 OTFDB = 1



Material Module Using Continuous Energy HDF5 Format AIS Database
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

In the following material module,

In the following material module, UO\ :sub:`2`\ and H\ :sub:`2`\O are defined separately using the \ **Mat**\ input card. The mass density of UO\ :sub:`2`\ is 
-10.196 g/cm\ :sup:`3`\ , with atomic ratios of U-235, U-238, and O-16 being 0.03 : 0.97 : 2.0. The atomic density of H\ :sub:`2`\O is 0.9997 bar\ :sup:`-1`\cm\ :sup:`-1`\, 
with atomic ratios of H-1 and O-16 being 2 : 1.

Through the Sab input card, a thermalization database (h-h2o) is specified for H\ :sub:`2`\O containing H-1 (1001.30c). All databases use the HDF5 format AIS nuclear 
database from ENDF-B8.0 (the program will automatically look for corresponding nuclear data files under the AISNucDatabase/ENDFB8.0 directory), and this example uses 
nuclear data at a temperature of 293.6 K.

In the **CeAce** input card, **pTable = 1** indicates the use of a probability table, **ErgBinHash = 1** indicates the use of a hash table to accelerate energy lookup, **DBRC = 1** indicates 
the use of the DBRC algorithm, **TMS = 0** indicates that the TMS algorithm is not used, and **OTFDB = 1** indicates that the Gaussian-Hermite integration method is used for on-the-fly 
Doppler broadening.

.. code-block:: c

    MATERIAL
    mat 1 -10.196
        U235 fraction = 0.03 endfversion = 8.0 tmp = 293.6
        U238 fraction = 0.97 endfversion = 8.0 tmp = 293.6
        O16  fraction = 2.0  endfversion = 8.0 tmp = 293.6
    mat 2 0.9997
        H1  fraction = 2.0   endfversion = 8.0 tmp = 293.6
        O16 fraction = 1.0   endfversion = 8.0 tmp = 293.6
    Sab 2
        h-h2o endfversion = 8.0 tmp = 293.6
    CEACE pTable = 1 ErgBinHash = 1 DBRC = 1 TMS = 0 OTFDB = 1



Material Module Using Multi-Group ACE Database
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

.. code-block:: c

  MATERIAL
  mat 1 -10.198
      92235.50m 6.9100E-03
      92238.50m 2.2062E-01
      8016.50m 4.5510E-01
  mat 2 -0.001
      8016.50m 3.76622E-5
  mat 3 -6.550
      40000.50m -98.2
  mat 4 -0.997
      1001.50m 6.6643E-02
      8016.50m 3.3334E-02
  MgAce ErgGrp = 30


In the material module above, .50m is a multigroup ACE cross section library with 30 groups. The number of energy
groups for the multigroup cross section is specified via the \ **ErgGrp** \ input option.

Material module using dynamic material changes
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

.. code-block:: c

 Material
 mat 1   -15.0
         92235.71c  -5
         92238.71c  -10
 DynamicMat  1     time=  0 200e-8 400e-8 500e-8 700e-8 900e-8 1000e-8
            Matdenvalue=-15 -15    -15    -15    -15    -15    -15
            Nucdenvalue=-5  -6.5   -14    -14    -6.5   -5     -5
                        -10 -8.5   -1     -1     -8.5   -10    -10


In the material module above, the parameters of the dynamic material that change with time are specified through the
\ **DynamicMat** \ input option. The \ **time** \ input option specifies the time point, \ **Matdenvalue** \ specifies the material
density corresponding to the time, the first row of the \ **Nucdenvalue** \ input option corresponds to the relative
fraction of nuclide 92235 over time, and the second row corresponds to the relative fraction of nuclide 92238 over
time. The program will determine the values between time points by interpolation. For example, when the fraction
of nuclide 92235 at the time point 100e-8 is required, the program will first determine the interpolation position
through time, and then interpolate the corresponding time parameter [-5,-6.5] of the nuclide.


Using the Continuously Varying Material Module
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

.. code-block:: c

 Material
 mat 1   -15.0
         92235.71c  -5
         92238.71c  -10
 cvmt 1 polytype = 4 dimension = 7 contitype = 0 order = -1 -1 1 2 coeffs = 0.0 1.0 2.0 3.0 4.0 5.0 7.0 6.0 8.0 9.0 10.0 11.0

In the material module above, the **cvmt** card is used to define parameters for a continuously varying material. The **polytype** card specifies 
that the variation function is a combination of Legendre and Zernike polynomials. The **dimension** card indicates that the geometry is a 3D 
cylindrical space. The **contitype** card sets the material to have a continuous density variation. The **order** card specifies the polynomial orders: 
no Legendre polynomial is defined in the x and y directions (-1 placeholders), while a first-order Legendre polynomial is applied in the 
z direction, and the Zernike polynomial is of the second order. The **coeffs** card provides the coefficients for the polynomial terms. In this case, 
there are (1+1)*(1+2+3)=12(1+1)*(1+2+3)=12 coefficients, which are explicitly defined.