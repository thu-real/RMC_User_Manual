.. _section_eng_su:

Sensitivity and Uncertainty Analysis (Enterprise Version Only)
====================================================================

Sandwich Method for Sensitivity and Uncertainty Calculation
----------------------------------------------------------------

The keff sensitivity analysis utilizes the iterated fission probability method, which can be applied in both traditional power iteration methods and
super-history methods. General sensitivity analysis employs collision history methods, GEAR-MC methods, and the new GPT formula method. To perform 
uncertainty calculations, sensitivity calculations must first be executed. A covariance index file, "covdir", is required, along with the covariance
database pointed to by the covdir directory.

Sensitivity Analysis
~~~~~~~~~~~~~~~~~~~~

An example of the input for keff sensitivity analysis:

.. code-block:: none

    Adjoint
    method <flag>
    KEFF
    BlockSize <Number>
    Nuclide  <zaid.xxx>   ……
    Reaction= <reaction_list_1, reaction _list_2, …>   ……
    Constrain <flag_1, flag_2, flag_3,…>
    groupoption <Number>
    Uncertainty
    OUTPUTINTERVAL <Number>
    OUTPUTTOTALSEN <flag>


Input Example for General Sensitivity Analysis (Collision Super-History Method, capable of analyzing reaction rate ratios and dynamic parameters on nuclear data):

.. code-block:: none

    Adjoint
    GENERAL
	BlockSize <Number>
	ResponseNu <zaid.xxx> 	<zaid.xxx>
    ResponseMT = <reaction_list_1, reaction _list_2>
    Ratio = <ratio_list_1, ratio _list_2>
    Nuclide  <zaid.xxx>   ……
    Reaction= <reaction_list_1, reaction _list_2, …>   ……
    groupoption <Number>
    Uncertainty
    OUTPUTINTERVAL <Number>
    Cell = <Number>

Input Example for General Sensitivity Analysis (Super-History GEAR-MC Method, capable of analyzing reaction rate ratios on nuclear data):

.. code-block:: none

    Adjoint
    GENERAL
	BlockSize <Number>
	GPTMETHOD <Number>
	ResponseNu <zaid.xxx> 	<zaid.xxx>
    ResponseMT = <reaction_list_1, reaction _list_2>
    Ratio = <ratio_list_1, ratio _list_2>
    Nuclide  <zaid.xxx>   ……
    Reaction= <reaction_list_1, reaction _list_2, …>   ……
    Constrain <flag_1, flag_2, flag_3,…>
	groupoption <Number>
    Uncertainty
    OUTPUTINTERVAL <Number>
    Cell = <Number>

Input Example for General Sensitivity Analysis (EGEAR-MC Method, capable of analyzing reaction rate ratios on geometric parameters):

.. code-block:: none

    Adjoint
    GENERAL
	GEOSENMETHOD <Number>
	BlockSize <Number>
	GPTMETHOD <Number>
	ResponseNu <zaid.xxx> 	<zaid.xxx>
    ResponseMT = <reaction_list_1, reaction _list_2>
    Ratio = <ratio_list_1, ratio _list_2>
    OUTPUTINTERVAL <Number>
    Surface <Number>
	Cell = <Number>

Input Example for General Sensitivity Analysis (Super-History New GPT Formula Method, capable of analyzing reaction rates on nuclear data):

.. code-block:: none

    Adjoint
    GENERAL
	BlockSize <Number>
	GPTMETHOD <Number>
	ReaRateType <Number>
    Nuclide  <zaid.xxx>   ……
    Reaction= <reaction_list_1, reaction _list_2, …>   ……
    Constrain <flag_1, flag_2, flag_3,…>
	groupoption <Number>
    Uncertainty
    OUTPUTINTERVAL <Number>
    Cell
	CellTallyID <Number> Cell =<Number>

Input Example for General Sensitivity Analysis (GPT-GE Method, capable of analyzing reaction rates on geometric parameters):

.. code-block:: none

    Adjoint
    GENERAL
	BlockSize <Number>
	GEOSENMETHOD <Number>
	ReaRateType <Number>
    OUTPUTINTERVAL <Number>
    Surface <Number>
	Cell = <Number>

Input Example for Random Sampling Method Perturbing a Single Nuclide:


.. code-block:: none

    Sampling
    SAMPLESIZE=<Number>
    Nuclide <zaid.xxx>
    Reaction= <reaction_list_1, reaction _list_2>
    GROUPOPTION <Number>



Input Example for Random Sampling Method Perturbing All Nuclides:


.. code-block:: none

    Sampling
    SAMPLESIZE = <Number>
    All
    GROUPOPTION <Number>


Where:

-  **Keff** \  is the keyword for the keff sensitivity analysis method input card. By default, this card is disabled.

.. code-block:: none

   Method   <flag>

Where:

-  **Method** \  is the keyword for the keff sensitivity analysis method input card.


-  **flag** \  specifies the keff sensitivity analysis method. \ **flag = 0** \   (default) corresponds to the traditional power iteration method, and 
   \ **flag = 1** \  corresponds to the superhistory method, which does not support OpenMP calculations. The general sensitivity analysis does not 
   require enabling this card, and geometric perturbations do not support the superhistory method.

.. code-block:: none

   GPTMETHOD   <Number>

Where:

-  **GPTMETHOD** \  is the keyword for the general response sensitivity analysis method input card.

-  **Number** \  specifies the general sensitivity analysis method. \ **Number = 0** \   (default) corresponds to the collision superhistory method,
   \ **flag = 1** \  corresponds to the superhistory GEAR-MC method, and \ **flag = 2** \  corresponds to the superhistory new GPT formula method. The general 
   sensitivity analysis does not support OpenMP calculations.

.. code-block:: none

   GEOSENMETHOD   <Number>

Where:

-  **GEOSENMETHOD** \  is the keyword for the general response sensitivity analysis method input card with respect to geometric parameters.

-  **Number** \  specifies the method for general response sensitivity analysis concerning geometric parameters. \ **Number = 0** \  (default) means that
   geometric sensitivity analysis is disabled, \ **flag = 1** \  corresponds to the EGEAR-MC method, and \ **flag = 2** \  corresponds to the GPT-GE method.
   The general response sensitivity analysis regarding geometric parameters does not support OpenMP calculations.

.. code-block:: none

   Surface   <Number>

Where:

-  **Surface** \  is the keyword for the general response sensitivity analysis method input card concerning geometric parameters.

-  **Number** \  specifies the specific geometric surface index being analyzed.

.. code-block:: none

   ReaRateType   <Number>

Where:

-  **ReaRateType** \  is the keyword for the general sensitivity analysis method input card.

-  **Number** \  specifies the type of reaction rate in the general response. \ **Number = 0** \  (default) does not represent any reaction rate,
   \ **flag = 1** \  corresponds to fission reaction rate, \ **flag = 2** \  corresponds to absorption reaction rate, and \ **flag = 3** \  corresponds to power.

.. code-block:: none

   BlockSize   <Number>

Where:

-  **BlockSize** \  specifies the keyword for the associated flux (iterated fission probability) or the convergence iterations of the generalized adjoint
   flux, with \ **Number** \  being the corresponding parameter, generally recommended to be set to 10.

.. code-block:: none

   Nuclide
          <zaid.xxx>
          <zaid.xxx>   ……

Where:

-  **Nuclide** \  is the keyword for the input card specifying the nuclides for sensitivity analysis.

-  **zaid.xxx** \  specifies the ACE cross-section database corresponding to the nuclide, where \ **zaid** \  is the nuclide ID, and the suffix \ **.xxx** \ 
   specifies the type of cross-section database.

.. code-block:: none

   ResponseNu
            <zaid.xxx>
            <zaid.xxx>

Where:

-  **ResponseNu** \  is the keyword for the input card defining the nuclides involved in the first type of response function for general sensitivity analysis.

-  **zaid.xxx** \  specifies the ACE cross-section database corresponding to the nuclide, where \ **zaid** \  is the nuclide ID, and the suffix \ **.xxx** \ 
   specifies the type of cross-section database.

.. code-block:: none

   Reaction
           <reaction_list_1, reaction _list_2, …>

-  **Reaction** \  specifies the types of reactions for each nuclide in the sensitivity analysis. Each nuclide can correspond to multiple reaction types,
   separated by commas, e.g., “Reaction= 16, 17, 102, -6, 107”. The correspondence between reaction types and their numbers can be found in the ENDF/B
   manual, with Table 7-1 providing common reaction type numbers. Note that the nuclides corresponding to the Reaction card must align with those in 
   the Nuclide card. Additionally, the Reaction card cannot be used simultaneously with the Uncertainty card.

   This is because when using the Uncertainty card, the considered reaction types are specified by the reaction types included in the covariance 
   database. When not using the Uncertainty card, the Reaction card must be included.

.. code-block:: none

   ResponseMT
             <reaction_list_1, reaction _list_2>


-  **ResponseMT** \  specifies the reaction types involved in the first type of response function for general sensitivity analysis for each nuclide.
   Each nuclide can correspond to multiple reaction types, separated by commas, e.g., “Reaction= 16, 17, 102, -6, 107”. The correspondence between
   reaction types and their numbers can be found in the ENDF/B manual, with Table 15-1 providing common reaction type numbers. Note that the nuclides 
   corresponding to the \ **ResponseMT** \  card must align with those in the \ **RespnseNu** \  card.

.. table:: Correspondence between Reaction Types and Numbers (Partial ENDF Reaction Types Listed)
  :name: reaction_mts_eng

  +-----------+---------------------+---------------------------------------------------------------+
  | MT Number | Reaction Type       | Remarks                                                       |
  +===========+=====================+===============================================================+
  | **1**     | Total Cross Section | For continuous energy ACE cross-sections, when the            |
  |           |                     | cross-section temperature does not match the fuel element     |
  |           |                     | temperature, the Doppler broadening is applied to the elastic |
  |           |                     | scattering cross-sections and the total cross-section. The    |
  |           |                     | adjusted cross-section is what is being reported here.        |
  +-----------+---------------------+---------------------------------------------------------------+
  | **-2**    | Absorption          | Does not include fission                                      |
  +-----------+---------------------+---------------------------------------------------------------+
  | **2**     | Elastic Scattering  |                                                               |
  +-----------+---------------------+---------------------------------------------------------------+
  | **4**     | Inelastic Scattering|                                                               |
  +-----------+---------------------+---------------------------------------------------------------+
  | **18**    | Total Fission       |                                                               |
  +-----------+---------------------+---------------------------------------------------------------+
  | **16**    | (n,2n)              | Applicable only to continuous energy ACE cross-sections       |
  +-----------+---------------------+                                                               +
  | **17**    | (n,3n)              |                                                               |
  +-----------+---------------------+                                                               +
  | **102**   | (n,γ)               |                                                               |
  +-----------+---------------------+                                                               +
  | **103**   | (n,p)               |                                                               |
  +-----------+---------------------+                                                               +
  | **107**   | (n,α)               |                                                               |
  +-----------+---------------------+---------------------------------------------------------------+
  | **452**   | Average Number of Fission Neutrons    |                                             |
  +-----------+---------------------+---------------------------------------------------------------+
  | **455**   | Average Number of Prompt Fission Neutrons         |                                 |
  +-----------+---------------------+---------------------------------------------------------------+
  | **456**   | Average Number of Delayed Fission Neutrons   |                                      |
  +-----------+---------------------+---------------------------------------------------------------+
  | **-1018** | Total Fission Neutron Spectrum        |                                             |
  +-----------+---------------------+---------------------------------------------------------------+
  | **-1455** | Prompt Fission Neutron Spectrum              |                                      |
  +-----------+---------------------+---------------------------------------------------------------+
  | **-1456** | Delayed Fission Neutron Spectrum      |                                             |
  +-----------+---------------------+---------------------------------------------------------------+

.. code-block:: none

   Ratio
        <ratio_list_1, ratio _list_2>

-  **Ratio** \  specifies the composition of the first type of response function in the general sensitivity analysis. This card needs to
   be used in conjunction with \ **RespnseNu** \  and \ **ResponseMT** \ . For example:

.. code-block:: none

   ResponseNu
     92235.60c
     92238.60c
   ResponseMT =
     18,
     18
   Ratio =
     2 1

In this case, ResponseNu defines two nuclides, 92235 and 92238, and ResponseMT defines their reaction type as MT=18, which is total fission. According 
to the order of appearance of the two nuclides, the total fission index for 92235 is 1, and for 92238 is 2. Thus, Ratio=2 1 means that index 2 is divided 
by index 1, forming a first-type response function, i.e., the fission reaction rate of U-238 divided by the fission reaction rate of U-235. In the 
current version, Ratio can only define one response.

.. code-block:: none

   GENERAL

Where:

-  **GENERAL** \  is the keyword for the general sensitivity analysis method input card. By default, this card is disabled.

.. code-block:: none

   Constrain   <flag_1, flag_2, flag_3,…>

Where:

-  **Constrain** \  is the keyword for the input card to compute the bound fission neutron spectrum.

-  **flag** \  specifies whether to compute the bound fission neutron spectrum. \ **flag = 0** \  (default) means not to compute the bound fission 
   neutron spectrum, and \ **flag = 1** \   means to compute it. It is recommended to compute the bound fission neutron spectrum for the analyzed 
   nuclides to obtain accurate results during uncertainty calculations.

-  General sensitivity analysis does not require enabling this card.

.. code-block:: none

   Cell = Cell_vector

Where:

-  **Cell** \  is the keyword for the cell input card. This card is used to define the geometric scope of the generalized response functions
   for the reaction rate ratios and the adjoint flux-weighted reaction rate ratios. For details, refer to the Cell card for the tally.


.. code-block:: none

   CellTallyID = IDNumber

Where:

-  **CellTallyID** \  is used to define the geometric region number for which the sensitivity coefficients of the reaction rate types of the generalized
   response function need to be output. IDNumber is the number of the cell tally for reference in the output. This card needs to be used in 
   conjunction with the Cell card to specify which cell's sensitivity coefficients are being statistically evaluated.

.. code-block:: none

   GroupOption   <flag>

-  **GroupOption** \  specifies the number of energy groups for the output of sensitivity coefficients. \ **flag = 0** \  means user-defined energy framework,
   with specific energy points specified through the Energybin card; \ **flag = 1** \  (default) calculates energy-integrated sensitivity coefficients; 
   \ **flag = 252** \  uses the program's built-in 252 energy grid; \ **flag=44** \  uses the program's built-in 44-group energy grid; \ **flag=56** \  uses 
   the program's built-in 56-group energy grid.

.. code-block:: none

   Energybin   <energy_1 energy_2 energy_3 …>

-  **Energybin** \  specifies the energy intervals for the output of sensitivity coefficients, with parameters as energy boundary points (MeV).
   For example, "\ **Energybin 6.25E-7  20** \  " indicates the tally range from 0.625 eV to 20 MeV, and from 20 MeV to positive infinity. The \ **Energybin** \  
   card can only be used when the \ **GroupOption** \  card's \ **flag=0** \ .

The output format of the sensitivity calculation is .Adjoint files.

.. code-block:: none

   ------------------ Nuclide = 92235, Reaction Type = 4 ------------------
   Group       Energy Bin         Ave            RE
     1         1.0000E-11     0.0000E+00     0.0000E+00
     2         3.0000E-09     0.0000E+00     0.0000E+00
     3         7.5000E-09     0.0000E+00     0.0000E+00
     ...
   ================================ sum of energy of first response  ================================
   ------------------ Nuclide = 92235, Reaction Type = 4 ------------------
   Group       Energy Bin         Ave            RE
     0         8.1900E+00     3.5373E-03     6.7842E-01

In the previous section, the first part represents the sensitivity coefficients for each energy interval, consistent with the energy intervals 
specified by the GroupOption card; the latter part is the energy-integrated sensitivity coefficients, which are the cumulative sensitivity 
coefficients across all energy intervals, and this value is independent of the chosen energy intervals. The standard deviations corresponding 
to the sensitivity coefficients are all relative errors.

Uncertainty Analysis
~~~~~~~~~~~~~~~~~~~~~

.. code-block:: none

   Uncertainty

-  **Uncertainty** \  is the keyword for the input card for uncertainty analysis; adding this option indicates that uncertainty analysis
   will be performed. During the uncertainty calculations, sensitivity coefficient calculations are performed first. The nuclides involved 
   in the sensitivity analysis are specified through the Nuclide card, while the reaction types for each involved nuclide will be determined 
   by reading the covariance index file "covdir", rather than through the Reaction card. When the \ **Uncerainty** \  card is enabled, the \ **flag** \  in
   the \ **GroupOption** \  card will be set internally to 44, meaning that the uncertainty will be calculated using the 44-group covariance database.

Output Options
~~~~~~~~~~~~~~~~~~~

.. code-block:: none

   OUTPUTINTERVAL   <Number>

Where:

-  **OUTPUTINTERVAL** \  specifying how many generations interval to output the result.

-  **Number** \  specifies the number of generations after which results are output. By default, sensitivity coefficients and uncertainty calculation
   results are only output once the calculation simulation has finished.

.. code-block:: none

   OutputTotalSen   <flag>

Where:

-   **OutputTotalSen** \  is the keyword for specifying whether to output the energy-integrated sensitivity coefficients.

-   **Flag** \  specifies whether to output the energy-integrated sensitivity coefficients. \ **flag = 0** \  (default value) means do not calculate 
    energy-integrated sensitivity coefficients, while \ **flag = 1** \   means to calculate them. The flag=1 option can only be used when the flag in
    the GroupOption is not equal to 1.

Uncertainty Calculation Using Random Sampling Method
-----------------------------------------------------

Uncertainty analysis using the random sampling method requires not only the covariance database index file “covdir” and the covariance database 
it points to, but also the disturbance database index file “samdir” and the disturbance database it points to.

.. code-block:: none

   Nuclide
          <zaid.xxx>
          <zaid.xxx> ……

Where:

-  **Nuclide** \  is the keyword for the input card specifying the nuclides for sensitivity analysis.

-  **zaid.xxx** \  specifies the ACE cross-section database corresponding to the nuclide, where \ **zaid** \  is the nuclide ID, and the suffix \ **.xxx** \ 
   specifies the type of cross-section database.

.. code-block:: none

   Reaction
           <reaction_list_1, reaction _list_2>


-  **Reaction** \  specifies the reaction types for the nuclides that need to be disturbed. Each nuclide can only analyze one pair of reaction types
   per calculation, separated by commas. The correspondence between reaction types and their identifiers can be found in the ENDF/B manual, where
   Table 15-1 provides some common reaction type identifiers. It is important to note that the nuclides corresponding to \ **Reaction** \  must be consistent
   with the \ **Nuclide** \   option card. Furthermore, if the reaction pair for the analyzed nuclide does not exist in the disturbance factor database, the
   program will not disturb it internally. Therefore, it is advisable to check whether the corresponding reaction pair exists in the disturbance
   factor database before using this option card.

.. code-block:: none

   SampleSize  <Number>

Where:

-  **SampleSize** \  is the keyword for specifying the disturbance sample size, with \ **Number** \   as the corresponding parameter. The maximum supported
   \ **Number** \  is currently 300.

.. code-block:: none

   All

Where:

-  **All** \  is the keyword for disturbing all nuclides and all reaction types (depending on the covariance database). Currently, the uncertainties in
   the covariance database include uncertainties in scattering cross-sections, absorption cross-sections, fission cross-sections, average number of
   fission neutrons, and fission neutron spectrum uncertainties. The current version of the random sampling method does not include uncertainties in
   the fission neutron spectrum. If the ALL mode is selected, there is no need to fill in the Nuclide and Reaction option cards.

.. code-block:: none

   GroupOption <EnergySize>

Where:

-  **GroupOption** \  is the keyword for specifying the number of energy groups (which must match the number of energy groups used in the disturbance
   factor database). EnergySize is the corresponding parameter. Currently, EnergySize only supports three parameters: 44, 56, and 252.

The output file for uncertainty analysis in .uncertainty format is as follows:

.. code-block:: none

   ================================ Uncertainty Information ================================
   the relative standard deviation of General response (% delta-R/R) due to cross-section covariance data is:
   2.1822E+01 +/- 2.6232E+01 %delta-R/R   //Represents the total uncertainty caused by nuclear data
                   covariance matrix
   nuclide-reaction      with       nuclide-reaction          %delta-R/R due to this matrix
       92235,  4                       92235,   4             1.4745E-01  +/- 6.8564E-04
       92235,  16                      92235,   16            0.0000E+00  +/- 0.0000E+00
       92235,  18                      92235,   18            3.3984E-01  +/- 3.4711E-02
       ...

Where the lines below the covariance matrix indicate the contributions of the covariance between reaction 1 and reaction 2 to the uncertainty of 
the response quantity as represented in the covdir file's covariance matrix.