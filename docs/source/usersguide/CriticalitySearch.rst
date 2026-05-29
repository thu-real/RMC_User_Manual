.. _section_eng_criticalitysearch:

Criticality Search (Enterprise Version Only)
==============================================

RMC supports criticality search in both criticality and burnup modes, including searches for nuclide density in materials and surface
position in geometry, where material nuclide density search is based on material density perturbation.

Material Search Module Input Card
------------------------------------

.. code-block:: none

    CriticalitySearch
    Materialsearch target=<param> error=<param> max=<maximum> perb=<differentialoperator id>
    Differentialoperator <id> [cell=<cell_vector>] [material=<mat id>] [nuclide=<nuc>] [variable=<param>]
        [estimator=<param>] [sourceperb=<param>] [batchcycle=<param>]  [outputbycycle=<param>]
        [crosssection=<param>] [response=<param>]


Where:

-  **CriticalitySearch** \  is the keyword for the criticality search module.

-  **materialsearch** \  is material search input card. Since this is a single-parameter search, this card can be used only once.

-  **target** \  is the target value of Keff for the search.

-  **error** \  is the search threshold. The search is considered complete when target-error < keff+-keff_std < target+error.

-  **max** \  is the maximum number of search iterations. After a criticality calculation is completed, the program will perform parameter 
   search calculations. The parameters obtained from the search will then be used for another criticality calculation, followed by 
   another round of parameter search, and so on, until convergence criteria are met. This parameter is set to prevent the program 
   from falling into an infinite loop in extreme cases where it fails to converge. It should be noted that the tally counting will 
   be turned off until the parameters meet the convergence criteria. If the user has enabled the tally function, the tally will be 
   re-enabled and another criticality calculation (active generations only) will be performed after the parameters converge. Therefore, 
   if \ **max = 1** \   and tally is not enabled, the calculation process will be: criticality calculation → parameter search → criticality 
   calculation. If \ **max = 1** \   and tally is enabled, the process will be: criticality calculation → parameter search → criticality calculation 
   → active generation criticality calculation. Notably, if \ **max = 0** \  is specified, the program will only perform one criticality calculation 
   and one search, with no tally counting.

-  **perb** \  is the ID of the differential operator method used for the search, corresponding to the **differentialoperator** input card. 
   Note that the corresponding **differentialoperator** must match, and the variable in the input card must be set to 1.

-  **differentialoperator** \  is the differential operator input card, the usage of which can be found in the material perturbation module instructions.

Material Search Module Input Example
------------------------------------------

The following example demonstrates a search on the density of nuclide ZAID 92235 in material 1 of cell 3, with a target value of 1.23,
a threshold of 0.02, a maximum of 5 iterations, using a collision estimator for the differential operator method, and including source 
perturbation. The batch cycle is set to 5, and only the last cycle outputs.

.. code-block:: none

    CRITICALITYSEARCH
    Materialsearch target=1.23 error=0.02 max=5 perb=1
    DifferentialOperator 1 cell = 3 
                       material = 1 
                       nuclide = 92235 
                       variable = 1
                       estimator = 0
                       order = 2
                       sourceperb = 1 
                       batchcycle = 5 
                       outputbycycle = 0
                       response = 1

Material Search Output File
------------------------------

The material search information is stored in the .critisearch file, which contains the first- and second-order perturbation coefficients for 
various differential operator methods, along with their relative standard deviations. During criticality iteration, the current keff and its 
standard deviation are provided, along with the atomic density of the searched nuclide before and after iteration, separated by an arrow (-->). 
For the first- and second-order perturbation coefficients for various differential operator methods, users can directly check the .Result.h5 file.

.. code-block:: none

    Cycle  Perb   First_Ave   First_Re    Second_Ave  Second_Re
    50     1      2.49773E-02 4.85185E+00 -4.22016E-02 8.69707E+00
    Iter   Nuc        keff                     Atom Density                   Gram Density
    0      92235.71c  1.218068 +- 0.012354     9.10578E-04 --> 1.34558E-03    -3.55398E-01 --> -5.25179E-01

    Cycle  Perb   First_Ave   First_Re    Second_Ave  Second_Re
    50     1      2.73379E-01 5.61727E-01 -8.78828E-01 6.68638E-01
    Iter   Nuc        keff                     Atom Density                   Gram Density
    1      92235.71c  1.301338 +- 0.014911     1.34558E-03 --> 9.94452E-04    -5.25179E-01 --> -3.88134E-01

    Cycle  Perb   First_Ave   First_Re    Second_Ave  Second_Re
    50     1      1.66851E-01 4.64208E-01 -4.77677E-01 6.47156E-01
    Iter   Nuc        keff                     Atom Density                   Gram Density
    2      92235.71c  1.253746 +- 0.011225     9.94452E-04 --> 8.52920E-04    -3.88134E-01 --> -3.32894E-01

    Cycle  Perb   First_Ave   First_Re    Second_Ave  Second_Re
    50     1      1.80428E-01 4.79098E-01 -2.63040E-01 1.30613E+00
    Iter   Nuc        keff                     Atom Density                   Gram Density
    3      92235.71c  1.196194 +- 0.011493     8.52920E-04 --> 1.01273E-03    -3.32894E-01 --> -3.95267E-01

    Cycle  Perb   First_Ave   First_Re    Second_Ave  Second_Re
    50     1      1.35776E-01 1.13751E+00 -5.65172E-01 4.01294E-01
    Iter   Nuc        keff                     Atom Density                   Gram Density
    4      92235.71c  1.275107 +- 0.012141     1.01273E-03 --> 6.76280E-04    -3.95267E-01 --> -2.63952E-01

    Cycle  Perb   First_Ave   First_Re    Second_Ave  Second_Re
    50     1      2.11684E-01 2.94337E-01 -3.85332E-01 6.42138E-01

Geometry Search Module Input Card
------------------------------------

.. code-block:: none

    CriticalitySearch
    Geometrysearch surface=<surf id> target=<param> error=<param> max=<maximum> method=<param> adaptive=<param>
           leftbound=<param> rightbound=<param> adjointtally=<param>


where,

-  **CriticalitySearch** \  is the keyword for the criticality search module.

-  **geometrysearch** \  is the input card for geometry search. Since it is a single-parameter search, this card can only be used once.

-  **surface** \  is the identifier for the search surface.

-  **target** \  is the target keff value for the search.

-  **error** \  is the threshold for the search. The search is considered complete when target-error < keff±keff_std < target+error.

-  **max** \  is the maximum number of iterations, used to prevent an infinite loop in extreme cases where convergence cannot be achieved.

-  **method** \  is the numerical iteration method for the search. 0 is Newton's method using geometric perturbation coefficients calculated 
   by the :ref:`mesh <section_eng_mesh>`. 1 is the bisection method. 2 is the false position method. 3 is Ridder's method.

-  **adaptive** \  is whether to adaptively adjust the number of active generations. 0 is to not use adaptive adjustment. 1 is to use adaptive adjustment.

-  **leftbound** \  is the initial left boundary for the search surface position. The search will stop if it goes below this value.

-  **rightbound** \   is the initial right boundary for the search surface position. The search will stop if it exceeds this value. The target value 
   should be estimated to lie between the left and right boundaries.

-  **adjointtally** \  is the tally number corresponding to the geometric perturbation coefficient used in Newton's method, calculated by the 
   :ref:`iterated fission probability method <section_eng_ifp>`.

Geometry Search Module Input and Output Example
--------------------------------------------------

The results of the geometry search are in the .critisearch file, including keff and standard deviation for each iteration. The "Parameter" is 
the search surface position for the next iteration, and "Cycle" is the total number of cycles used in the current iteration.

In the following example, the position of surface 1 is searched between 5 cm and 10 cm, with a target keff between 0.99 and 1.01, using the 
Ridder method with adaptive cycle adjustment. The final surface position is 8.74888 cm, and keff is 1.001257 ± 0.001635.

.. code-block:: none

    Geometrysearch
    surface=1
    target=1
    error=0.01
    method=3
    adaptive=1
    leftbound=5
    right=10

.. code-block:: none

    Final Keff: 1.001257      Standard Deviation: 0.001635

    Iter   SurfId  keff                    Parameter      Cycle
    0      1       0.602600 +- 0.000280    1.00000E+01    300
    Iter   SurfId  keff                    Parameter      Cycle
    1      1       1.115286 +- 0.000489    7.50000E+00    300
    Iter   SurfId  keff                    Parameter      Cycle
    2      1       0.876570 +- 0.001635    8.74888E+00    110

