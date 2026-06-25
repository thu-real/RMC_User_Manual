.. _section_eng_ais_database_usage:

AIS Database Usage
====================

The RMC code supports the use of AIS nuclear databases in HDF5 format. This is a newly developed nuclear database
format designed to replace the ACE-format database, achieving significant breakthroughs in readability, extensibility,
and code maintainability.
Currently, this database supports continuous-energy neutron transport calculations for criticality source and fixed source,
and multi-group neutron transport calculations for criticality. For continuous-energy neutrons, the supported
features include: all online cross-section processing functions, cell-, surface-, and mesh-type tally (type 1–8),
cross-section tally, point tally, material perturbations, the UFS (Uniform Fission Site) method, source convergence
diagnostics and acceleration, as well as sensitivity and uncertainty analysis. When using this feature, the input syntax
for all material-related cards is modified accordingly.
All HDF5-format AIS databases are integrated into RMC within this module.
When performing calculations using this function, the HDF5‑format AIS nuclear database is required and
must be placed in the same folder as the executable file, with the directory structure: /AISNucDatabase/ENDFB XX.
The HDF5‑format AIS nuclear database can be obtained in two ways:  1.Manually convert the ACE database (requir
ed for the calculation) into the corresponding AIS database using the AISG software;  2.Download the compressed data
packages for ENDF/B‑7.1, 8.0, and 5.0 from the repository thu‑real/AISNuclearDatabase on the GitLab platform,
and extract them to the appropriate location.

.. _section_eng_aismat_mat:

Material Input Card Based on HDF5 Format AIS Database
--------------------------------------------------------

Currently, the HDF5-format AIS database supports both continuous-energy and multi-group calculations. Functionally, it
currently supports neutron transport calculations for criticality source and fixed source. The supported features include:
all online cross-section processing functions; cell, surface, and mesh tally (type 1–8), cross-section tally, point tally,
material perturbations, the UFS method and source convergence diagnostics and acceleration.

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
   Among them, ENDFVERSION=7.1 and ENDFVERSION=8.0 correspond to the continuous-energy neutron databases of ENDF/B‑7.1 and ENDF/B‑8.0,
   respectively, while ENDFVERSION=5 represents the multi‑group neutron nuclear database of ENDF/B‑5.0.

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

.. _section_eng_aismat_mg:

Multi-group HDF5-format AIS Cross-Section Related Input Card
-------------------------------------------------------------

If the nuclides in the material input option use multi-group HDF5-format AIS cross sections, \*the user must use the following
input card to specify the relevant parameter options for the multi-group cross sections.*\

.. code-block:: none

  MgAce [ErgGrp = <grp_neu>]

where,

-  **MgAce** \ is the keyword for the multi-group ACE cross-section related input option;

-  **ErgGrp** \ specifies the group number of multi-group neutron.

.. _section_eng_aismat_su:

Sensitivity and Uncertainty Calculation based on HDF5-format AIS database
----------------------------------------------------------------------------

Example of Input for Sensitivity Analysis：

.. code-block:: none

    Adjoint
    GENERAL
	BlockSize <Number>
	GPTMETHOD <Number>
	ResponseNu <IsoSym> <ENDFVERSION=...> <IsoSym> <ENDFVERSION=...>
    ResponseMT = <reaction_list_1, reaction _list_2>
    Ratio = <ratio_list_1, ratio _list_2>
    Nuclide <IsoSym> <ENDFVERSION=...>   ……
    Reaction= <reaction_list_1, reaction _list_2, …>   ……
    Constrain <flag_1, flag_2, flag_3,…>
	groupoption <Number>
    Uncertainty
    OUTPUTINTERVAL <Number>
    Cell = <Number>

For the specific meanings of each tab, please refer to :ref:`section_eng_su` module. Using the AIS database only
The writing of ResponseNu and Nuclide cards has been changed to be consistent with :ref:`section_eng_mat_mat` module.


Example of Input for Uncertainty Analysis of Random Sampling Method：

.. code-block:: none

    Sampling
    SAMPLESIZE=<Number>
    Nuclide <IsoSym> <ENDFVERSION=...>
    Reaction= <reaction_list_1, reaction _list_2>
    GROUPOPTION <Number>

For the specific meanings of each tab, please refer to :ref:`section_eng_su` module. Using the AIS database only
The writing of Nuclide cards has been changed to be consistent with the :ref:`section_eng_mat_mat` module.


.. _section_eng_aismat_example:

Material module input example
--------------------------------

Material Module Using Continuous Energy AIS Database input example
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~


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


Material module using multi group AIS database input example
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

In the following material module, three materials, U, He, and H\ :sub:`2`\ O, are first defined via the \ **Mat**\ input card.
The atomic density of U is 6.86463E‑02 bar\ :sup:`-1`\ cm\ :sup:`-1`\, and the atomic ratios of U235, U235, and U236 are 6.11864e‑06 : 7.18132E‑04 : 3.29861E‑06.
The atomic density of He is 2.68714E‑05 bar\ :sup:`-1`\ cm\ :sup:`-1`\.
The mass density of H\ :sub:`2`\ O is 0.661979 g/cm\ :sub:`3`\, and the atomic ratio of H1 to O16 is 2 : 1.
The number of energy groups is defined as 30 via the \ **Mgace**\ input card.
All databases use the HDF5‑format AIS nuclear database from ENDF/B‑5.0 (the program will automatically search for the corresponding nuclear data files under the database path AISNucDatabase/ENDFB5.0).
In this example, nuclear data at a temperature of 293.6K are used for all materials.

.. code-block:: c

    MATERIAL
    mat 1 6.86463E-02
        U234 fraction = 6.11864e-06 ENDFVersion = 5 tmp=293.6
        U235 fraction = 7.18132E-04 ENDFVersion = 5 tmp=293.6
        U236 fraction = 3.29861E-06 ENDFVersion = 5 tmp=293.6
    mat 2 2.68714E-05
        He4  fraction = 2.68714E-05 ENDFVersion = 5 tmp=293.6
    mat 3 -0.661979
        O16 fraction = 1          ENDFVersion = 5  tmp=293.6
        H1 fraction = 2           ENDFVersion = 5  tmp=293.6
    Mgace erggrp=30

In the above material module, through the\ **ErgGrp**\ tab, the number of energy groups for the multi‑group cross sections is specified.

Sensitivity analysis module using AIS database input example
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

In the following sensitivity analysis calculation module, \ **GENERAL**\ is specified as generalized response analysis;
\ **GPTMETHOD**\ = 1 selects the superhistory GEAR‑MC method; \ **BlockSize**\ = 10 is the block size for the convergence
iteration of the adjoint flux (generalized adjoint flux); \ **ResponseNu**\ defines the nuclides involved in the response function,
here U234 and U235 in AIS format; \ **ResponseMT**\ = 18 specifies the reaction types for the above nuclides in order,
both being 18 (total fission); \ **Ratio**\ = 1 2 indicates that the response is sequence number 1 divided by sequence number 2,
i.e., U234 fission rate / U235 fission rate; \ **Nuclide**\ lists the nuclides for which sensitivity is to be analyzed,
also U234 and U235 in AIS format; \ **Reaction**\ specifies, in the order of the nuclides, the reaction type(s) to be analyzed for each nuclide;
\ **groupoption**\ = 44 uses the built‑in 44‑group energy grid to output sensitivity coefficients; \ **OUTPUTINTERVAL**\ = 10
outputs intermediate results every 10 generations; \ **OUTPUTTOTALSEN**\ = 1 requests additional output of the
energy‑integrated total sensitivity coefficients; \ **UNCERTAINTY**\ enables uncertainty analysis; \ **Cell**\ defines the
geometric domain for response and sensitivity calculations, here specified as cell number 1.

.. code-block:: c

    Adjoint
    GENERAL
    GPTMETHOD 1
    BlockSize 10
    ResponseNu
        U234  ENDFVersion = 7.1
        U235  ENDFVersion = 7.1
    ResponseMT =
        18,
        18
    Ratio =
        1 2
    Nuclide
        U234  ENDFVersion = 7.1
        U235  ENDFVersion = 7.1
    Reaction=
        2 18,
        4  18  102
    groupoption 44
    OUTPUTINTERVAL 10
    OUTPUTTOTALSEN 1
    Cell
    CellTallyID 1 Cell = 1

Uncertainty analysis module using AIS database input example
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

In the following uncertainty analysis calculation module, \ **SAMPLESIZE**\ = 50 specifies that 50 perturbed samples are generated;
\ **Nuclide**\ lists the nuclides U234, U235, and U238 whose cross sections are to be perturbed; \ **Reaction**\ specifies,
in the order of the nuclides, the reaction type(s) to be analyzed for each nuclide (18 perturbs only the fission cross section);
\ **groupoption**\ = 44 uses the built‑in 44‑group perturbation factor database.

.. code-block:: c

    Sampling
    SAMPLESIZE=50
    Nuclide
        U234  ENDFVersion = 7.1
        U235  ENDFVersion = 7.1
        U238  ENDFVersion = 7.1
    Reaction=
        18,
        18,
        18
    groupoption 44



