.. _section_eng_variance_reduction:

Variance Reduction
====================

RMC supports variance reduction calculation functions, including region importance, source biasing, and weight window techniques. 
Currently, both region importance and weight window techniques are used only in fixed source mode. The weight window techniques 
include cell weight windows and mesh weight windows. For the same type of particle (neutrons, photons, electrons), only one of the 
three variance reduction methods (region importance, cell weight window, or mesh weight window) can be used at the same time.

Region Importance Input Option
-------------------------------

In the cell information card, users may add the geometric importance option \ **imp** \ , which is divided into neutron geometric 
importance \ **imp:n** \ , photon geometric importance \ **imp:p** \ , and electron geometric importance \ **imp:e** \ . For example: imp:n=0, 
imp:n=1, imp:n=2, imp:p=0, imp:p=1, imp:p=2, imp:e=0, imp:e=1, imp:e=2, etc.

Weight Window Input Option
-------------------------------

.. code-block:: none

    WeightWindow
    WWN:N  W_1  W_2  W_3  W_4 … W_n 
    WWMESH:N  W_1  W_2  W_3  W_4 … W_n
    WWP:N  WUPN WSUV 
    [WeightWindowMESH:Geometry Axis Vector Origin 
  Scope Bound BoundX BoundY BoundZ ScopeX ScopeY ScopeZ]

The example is for the neutron's cell weight window and mesh weight window input card. Among them:

-  **WeightWindow** \  is the keyword for the variance reduction weight window input card;

-  **WWN:N** \  is the neutron's cell weight window card, where W1, W2, W3, etc., represent the lower limits of the weight windows for each cell. 
   If the cell weight window card is being used for photons, replace the keyword with WWN:P. If the cell weight window card is being used for electrons, 
   replace the keyword with WWN:E;

-  **WWMESH:N** \  is the neutron's mesh weight window card, where W1, W2, W3, etc., represent the lower limits of the weight windows for each mesh. 
   The definitions of the meshes are in the mesh tally section. The current code supports non-uniform meshes and non-uniform cylindrical meshes. 
   If the mesh weight window card is being used for photons, replace the keyword with \ **WWMESH:P** \ . If the mesh weight window card is being used for 
   electrons, replace the keyword with \ **WWMESH:E** \ ; In current version of RMC, the default method of mesh-based weight window is Track-based weight window algorithm.

-  **WWP:N** \  is the neutron's weight window parameter card, \ **WUPN** \  indicates the ratio of the weight window upper limit to the lower limit, 
   requiring WUPN > 2, with a default value of 5. \ **WSUV** \  indicates the ratio of the weight of surviving particles to the weight window lower limit, 
   requiring 1 < WSURVN < WUPN, with a default value of 3. If the weight window card is being used for photons, replace the keyword with \ **WWP:P** \ . 
   If the weight window card is being used for electrons, replace the keyword with \ **WWP:E** \ ;

-  Note that only one type of particle weight window card can be used at the same time. For example, for neutrons, only one of \ **WWN:N** \  or 
   \ **WWMESH:N** \  needs to be defined. If WWP:N is not defined, it will use the default value.

-  **WeightWindowMESH**\ The mesh used for weight window will be used in a very similar way as in the Meshtally, which also includes the various defining factors of a mesh grid.

-  **Geometry** \  card specifies the type of coordinate system: 1 for Cartesian coordinates, 2 for cylindrical coordinates, with the default being 1.

-  **Axis** \  card specifies the z-axis direction in cylindrical coordinates; it is not defined for Cartesian coordinates.

-  **Vector** \  card, along with the \ **Axis** \  vector, forms the plane where \ **φ = 0** \  . The \ **Vector** \   and \ **Axis** \  can be non-perpendicular 
   but cannot be parallel; it is not defined for Cartesian coordinates.

-  **Origin** \  card specifies the origin coordinates in cylindrical coordinates; it is not defined for Cartesian coordinates.

-  **Scope** \   card specifies the number of meshes in the x, y, and z directions. Specifically, the parameter “-1” indicates that there is only one 
   infinite mesh in that direction (Note: in the Scope card within Universe repeated geometry, a parameter of 1 indicates that there is only one 
   infinite mesh in that direction).

-  **Bound** \  card specifies the boundary range of the mesh in the x, y, and z directions, formatted as “Bound = x_min x_max y_min y_max z_min z_max”. 
   If there is only one mesh in a direction, the corresponding parameters in the \ **Bound** \   card are meaningless.

-  **BoundX / BoundY / BoundZ** \  cards specify the coarse mesh boundary sequence for non-uniform meshes in the x, y, and z directions, respectively. 
   For example, "BoundX = 1.0 3.0 7.0" indicates there are three coarse mesh boundaries in the x direction, which are 1.0, 3.0, and 7.0. 
   Note: The coarse mesh boundary sequences in each direction must be monotonically increasing.

-  **ScopeX \ ScopeY \ ScopeZ** \  cards specify the fine mesh tally sequences for non-uniform meshes in the x, y, and z directions, respectively. 
   For example, "ScopeX = 2 8" indicates that there are a total of two coarse meshes in the x direction, with 2 and 8 fine meshes within each 
   coarse mesh, respectively. Note: The number of coarse mesh boundaries must be one more than the number of coarse meshes, and **RMC does 
   not currently support non-uniform meshes that have only one layer of infinite mesh in any direction**.


Compatibility with MCNP Mesh Weight Window Input Card
--------------------------------------------------------

Currently, RMC is compatible with MCNP format weight window files, allowing the use of MCNP variance reduction parameters WWE, WWN, WWP, and WWINP 
cards, but not supporting the WWT card. \ **MCNPWeightWindow** \  is the keyword for the compatible MCNP weight window input card parameters;

.. code-block:: none

    MCNPWeightWindow
    WWE:N  E_1  E_2  E_3  E_4 … E_j
    WWN1:N W_11  W_12  W_13  W_14 … W_1j
    WWN2:N W_21  W_22  W_23  W_24 … W_2j
    [WWP:N WUPN WSURVN MXSPLN MWHERE SWITCHN MTIME WNORM ETSPLT]
    [WWINP  FileName]

-  **WWE:N** \  is the neutron's cell weight window card, where E_1, E_2, E_3, E_4 … E_j represent the energy intervals for each cell. The minimum 
   energy is not input on the WWE card but is defined as zero.

-  **WWNi:N** \  specifies the lower limits of the weight window related to space and energy in the cell. If the weight window is energy-dependent 
   (i.e., there are weight window splits for particles of different energies), it must be used with the WWE card. The weight limits for particles 
   are always absolute values, not relative. If both WWN and WWE cards are used, the user must specify a weight window lower limit for each energy 
   for each cell. This means that if the WWE card specifies n energy bins and there are m cells in this example, n weight window lower limits must 
   be input after each WWNi:N, corresponding to the n energy lower limits in this cell.

-  **WWP:N** \  is the neutron weight window parameter card. \ **WUPN** \  indicates the multiple of the upper and lower limits of the weight window, 
   with a requirement of WUPN > 2, and the default value is 5. \ **WSUV** \  represents the multiple of the surviving particle weight and the lower weight 
   window limit, requiring 1 < WSURVN < WUPN, with the default value of 3. \ **MXSPLN** \  greater than 1 indicates the maximum number of particle splits. 
   \ **MWHERE** \  specifies where the weight window is used, currently only supporting 0, meaning the weight window is used at particle crossing surfaces 
   and collisions. \ **SWITCHN** \  specifies where the lower limit of the weight window is obtained, currently supporting only <0 and =0. <0 means the 
   lower limit is obtained from WWINP, and =0 means it is obtained from WWN. It is important to note that this parameter must match the type 
   of card used. When <0, WWINP must be used, and when =0, WWN must be used. \ **MTIME** \  indicates how to interpret the parameters in the WWE card: =0 
   means the numbers in WWE represent the energy grid of the weight window, and >0 means the numbers in WWE represent the time grid of the 
   weight window. Currently, only MTIME = 0 is supported. \ **WNORM** \  represents the weight window multiplier, which is currently not supported 
   in RMC and must be set to 0. \ **ETSPLT** \  indicates the use of ESPLT and TSPLT, which are not supported in RMC, so this parameter must be set to 0.
   
-  **WWINP** \  is the card that allows RMC to read the MCNP weight window file format. The FileName is specified as the MCNP weight window format 
   file. It should be noted that when using WWINP, a grid weight window must be used, which generates a mesh tally numbered 99, and WWINP cannot 
   be used together with WWN.


WWN Card and WWE Card (Neutron) Example

.. code-block:: none

    MCNPWeightWindow
    WWE:N     e1  e2  e3
    WWN1:N    w11  w12  w13  w14
    WWN2:N    w21  w22  w23  w24
    WWN3:N    w31  w32  w33  w34

This example defines the lower limits of neutron weight windows for 4 cells across 3 energy ranges. For the energy bin 0-e1, the lower weight 
window limits for cells 1 to 4 are w11, w12, w13, and w14, respectively, and so on.

Input Card for MCNP-Compatible Weight Window Generator
--------------------------------------------------------

RMC currently supports the MCNP-compatible weight window generator and allows the use of the MCNP weight window generation 
cards WWG and WWGE. **MCNPWeightWindow** \  is the keyword for the MCNP-compatible weight window generator input card. WWG and WWGE 
cards cannot be used simultaneously with any type of weight window parameters (WWE, WWN, WWP, WWINP cards). Also, the weight
window generator in RMC currently generates weight windows for all particles involved in the current calculation process by default.The output weight window card will be named as [inp_file_name].wwout

.. code-block:: none

    MCNPWeightWindow
    WWG  It  Ic  Wg  J1  J2  J3  J4  IE
    WWGE E_1 E_2 … E_j  //j is any positive integer


-  **WWG** \  is the automatic weight window lower limit generator parameter card. 

-  **It**\ refers to the tally number of the reference CellTally. 

-  **Ic**\ specifies whether the weight window generator is based on cells or grids. If Ic > 0, the cell-based 
   weight window generator is used, with the reference cell number being Ic, and the REF card cannot be used in this case. 
   If Ic < 0, the grid-based weight window generator is used, with the absolute value of Ic being the reference grid tally number. 
   
-  **Wg**\ is the lower limit of the weight window generated from the reference CellTally or MeshTally. If Wg = 0, the lower weight window 
   limit for the reference CellTally or MeshTally is 0.5 times the source particle weight. The default value of Wg is 0. J1-J4 
   have no significance and are retained for compatibility with the MCNP format. When using a grid-based weight window generator 
   (Ic = 0), a MeshTally must be specified, and the grid defined by this MeshTally is the weight window grid. 

-  **IE**\ specifies the input 
   method for the WWGE card, currently only supporting IE = 0, which means the WWGE card defines the energy grid.

-  **WWGE** \  is the energy grid input card for the automatic weight window lower limit generator. E_1, E_2, E_3, E_4 … E_j 
   are the upper boundaries of the energy grid. By default, the lowest energy is 0 during weight window generation. j can be 
   up to 10,000; if exceeded, the values will be truncated.

Example of WWG Card and WWGE Card

.. code-block:: none

    MCNPWeightWindow
    WWG 1 1 0.001 0 0 0 0 0
    WWGE:N 0.01 0.1 1 5

This example defines a WWG card based on the MCNP format. The weight window is generated for the tally results of the cell tally 
numbered 1, with the reference cell being 1, the lower limit of the weight window being 0.0001, and the energy grid being 0.01, 0.1, 1, and 5 MeV.

Source Biasing Input Card
-------------------------------

The source biasing function can be specified in the source description module. To perform biased sampling between different sources, 
you can use the \ **Source** \  option card's \ **BiasFrac** \  parameter. To perform biased sampling within a specific source distribution, you can 
use the \ **Bias** \ parameter in the \ **Distribution** \  option card.
