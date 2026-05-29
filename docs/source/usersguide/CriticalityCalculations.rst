.. _section_eng_criticality:

Criticality Calculations
============================

Monte Carlo criticality calculations use the fission source iteration method, that is, the fission sources
generated in the current generation are used as the initial sources for the next generation. The user needs
to specify the basic parameters for source iteration in the input file, including the initial effective
multiplication factor (keff), the number of neutrons per generation, and the number of neutron generations, as
well as the initial fission source distribution.

In the criticality calculation module, users can also select the type and parameters of the random number
generator, as well as the parallel mode of parallel critical calculation.

.. _section_eng_crit_poweriter:

Source Iteration Parameters
---------------------------

.. code-block:: none

    PowerIter [Keff0 = <keff0>] [Population = <N Mi Mt>] [Batchnum = <Mb>]



where,

-  **PowerIter** is the keyword for the source iteration input card.

-  **Keff0** is an input option that sets the initial effective multiplication coefficient, and the default value is
   **keff0 = 1**. The program requires the user to enter the initial effective multiplication coefficient
   0.1 < keff0 < 10.

-  **Population** is an input option that sets the number of neutrons per generation **N**,
   the number of inactive generations (M\ :sub:`i`）, and the total number of generations(M\ :sub:`t`). Accordingly,
   the number of active generations for Monte Carlo critical calculations is M\ :sub:`a` =  M\ :sub:`t` – M\ :sub:`i`,
   and the total number of active neutrons is M = N × M\ :sub:`a`.

-  **Batchnum** \ is used to reduce the tally merging operations in parallel critical calculations, compressing
   M\ :sub:`a` active generations into M\ :sub:`b` groups and merging data every M\ :sub:`a` / M\ :sub:`b`
   active generations. The default value is M\ :sub:`b` = M\ :sub:`a`.

.. _section_eng_crit_ufsmesh:

UFS Mesh Parameters
-------------------

.. code-block:: none

    	UfsMesh [UfsType = <type>] [Scope = <params>] [Bound = <params>] [AutoCutWgt = <Mt>]

where,

-  **UfsMesh** is the keyword for the UFS Mesh input card.

-  **UfsType** sets the UFS method type. **UfsType = 0** (default value) means the UFS method is not used,
   **UfsType = 1** means using the uniform fission source method, **UfsType = 2** means using the biased
   fission source method (since the biased fission source method directly uses the maximum number of
   neutrons in the mesh divided by the number of neutrons in the corresponding mesh, the uniform result
   obtained is not obvious, so this method is not recommended)

-  **Scope** specifies the number of mesh cells in the x, y, and z directions. In particular, a parameter of
   "-1" means there is only one layer of infinite mesh cell in that direction.

-  **Bound** specifies the number of mesh cells in the x, y, and z directions. For example, "Bound =
   x_min x_max y_min y_max z_min z_max". If there is only one layer of mesh cell in a certain direction,
   the corresponding parameters in the **Bound** have no practical meaning.

-  **AutoCutWgt** specifies whether to use automatic change of neutron cutoff weight. If M\ :sub:`t` = 0, the
   program sets the neutron cutoff weight to 0.01. If M\ :sub:`t` = 1, the program automatically changes the
   neutron cutoff weight. It is recommended to use M\ :sub:`t` = 1. When UfsType = 2, it is recommended to use
   M\ :sub:`t` = 1, otherwise a warning will be reported.

.. _section_eng_crit_initsrc:

Initial fission source
----------------------

.. code-block:: none

  InitSrc [<type> = {params}]

where,

-  **InitSrc** is the keyword for the initial fission source input card.

-  **type** is the keyword of the fission source type, and **params** is the corresponding parameter. The initial
   fission source distribution supported by RMC includes three types: point source, uniform source and universal
   source; see :numref:`source_table_eng` for further details. In addition to the initial sources mentioned above, 
   the program also supports user input of “HDF5 = 1”, which allows for reading fission source parameters from the 
   “convergent_source.h5” file to accelerate source convergence.

   .. note:: This method is typically applied during coupled calculations. By reading the converged fission source output 
      from the previous iteration step as the initial source for the next iteration step, it can reduce the number of source 
      iterations and speed up source convergence.

.. table:: Distribution of initial fission sources supported by RMC
  :name: source_table_eng

  +--------------------+----------------------------------+---------------------------------------------------------------------------------------+
  | Keyword            | Description                      | Parameter                                                                             |
  +====================+==================================+=======================================================================================+
  | **Point**          | Isolated point source            | x\ :sub:`1` y\ :sub:`1` z\ :sub:`1` x\ :sub:`2` y\ :sub:`2` z\ :sub:`2` …             |
  +--------------------+----------------------------------+---------------------------------------------------------------------------------------+
  | **Slab**           | A rectangular uniform source     | x\ :sub:`min` x\ :sub:`max` y\ :sub:`min` y\ :sub:`max` z\ :sub:`min` z\ :sub:`max`   |
  |                    | parallel to the coordinate axis  |                                                                                       |
  +--------------------+----------------------------------+---------------------------------------------------------------------------------------+
  | **Sph**            | Spherical uniform source         | x y z R                                                                               |
  +--------------------+----------------------------------+---------------------------------------------------------------------------------------+
  | **Cyl/x**          | Cylindrical uniform source       | y z R x\ :sub:`min` x\ :sub:`max`                                                     |
  |                    | parallel to the x-axis           |                                                                                       |
  +--------------------+----------------------------------+---------------------------------------------------------------------------------------+
  | **Cyl/y**          | Cylindrical uniform source       | x z R y\ :sub:`min` y\ :sub:`max`                                                     |
  |                    | parallel to the y-axis           |                                                                                       |
  +--------------------+----------------------------------+---------------------------------------------------------------------------------------+
  | **Cyl/z**          | Cylindrical uniform source       | x y R z\ :sub:`min` z\ :sub:`max`                                                     |
  |                    | parallel to the z-axis           |                                                                                       |
  +--------------------+----------------------------------+---------------------------------------------------------------------------------------+
  | **ExternalSource** | Universal Source                 | x                                                                                     |
  +--------------------+----------------------------------+---------------------------------------------------------------------------------------+
  | **HDF5**           | HDF5 Convergent Fission Source   | 1                                                                                     |
  +--------------------+----------------------------------+---------------------------------------------------------------------------------------+
.. _section_eng_crit_rng:


External Source Subroutines
---------------------------------------------

When a description of a general source requires a higher degree of flexibility in terms of description, an external
source subroutine can be used. The input card for this section is:

.. code-block:: none

  SOURCESUB [SOURCE = <source>] [PTMOD = <PTMod>]

For details, see the section External Source Subroutine for Fixed Source Calculation. Note that this card must be
defined after the **InitSrc** input option, and it can only be used with the general source **ExternalSource** is in use.



Random Number Generator
-----------------------

RMC contains three different types of random number generators. For general users, it is recommended to use the
default random number generator type and parameters. Advanced users may need to use specific random number parameters,
for example, use different random number seeds for the same case to obtain independent calculation results. RMC
provides input card for custom random number generators:

.. code-block:: none

  RNG [Type = <type>] [Seed = <seed>] [Stride = <stride>]


where,

-  **RNG** is the keyword for the random number generator input card.

-  **Type** specifies the type of random number generator. **Type = 1** is a 48-bit multiplicative linear
   congruential random number generator, **Type =  2** (default) is a 64-bit multiplicative
   linear congruential random number generator, **Type = 3** is a 64-bit hybrid linear congruential
   random number generator, and **Type = 5** is a 128-bit random number generator.

-  **Seed** specifies the initialization seed of the random number generator, which can be any positive odd number.
   The default value is **Seed = 1**.

-  **Stride** is used to set the length of the random number of each neutron history when parallel computing
   is performed. It is only recommended for advanced users. The default value is **Stride = 10000**.


.. _section_eng_crit_cutoff:

Truncation Conditions
---------------------

.. code-block:: none

  CutOff [MaxLost=<maxLost>]
         [MinWeight=<minWeightN minWeightP>]
         [MaxWeight=<maxWeightP>]

where,

-  **CutOff** is the keyword for the truncation condition input card.

-  **MaxLost** specifies the maximum number of particles that the program allows to be lost. The default value is 10.

-  **MinWeight** specifies the lower limit of the weight in the transport process. **minWeightN** specifies the lower
   limit of the weight of neutrons, with the default value as 0.25, and **minWeightP** specifies the lower limit of the
   weight of photons, with the default value as 0.6. After the weight window variance reduction function is
   turned on, the input here is invalid

-  **MaxWeightP** specifies the upper limit of the weight of photons in the transport process. The default value is 1.4.

.. _section_eng_crit_parallelbank:

Parallel Criticality Calculation Mode
---------------------------------------

In parallel critical calculations, and considering load balancing, it is necessary to collect and redistribute the
fission source neutrons on each process. Traditional Monte Carlo programs generally use the master-slave mode,
which has low collection and distribution efficiency. RMC uses the peer-to-peer mode (slave-slave) to
improve parallel efficiency. The input option for selecting the parallel critical calculation mode is:

.. code-block:: none

  ParallelBank <flag>

where,

-  **ParallelBank** is the keyword for the Parallel Criticality Calculation Mode input card.

-  **flag** specifies the parallel mode. **flag = 0** represents master-slave mode, while **flag = 1** (default)
   represents peer-to-peer mode.

Fission Matrix Method for Solving Critical Adjoint Flux
--------------------------------------------------------

.. code-block:: none

    MGAdjFisMatrix [Energy=<enenrgy>]  [Scope=<scope>] [Bound=<bound>]


where,

-  **MGAdjFisMatrix** is the keyword for the input card for the fission matrix method to solve the critical
   adjoint flux.

-  **Energy** specifies energy cutoff points for the adjoint flux.

-  **Scope**  specifies the number of mesh cells in the x, y, and z directions. In particular, a parameter
   of "-1" means there is only one layer of infinite mesh cell in that direction (Note: in the Scope tab of the
   Universe Repeated Geometry, a parameter of 1 means there is only one layer of infinite mesh cell in that direction).

-  **Bound** specifies the boundary range of the mesh in the x, y, and z directions, in the form of "Bound =
   x_min x_max y_min y_max z_min z_max". If there is only one layer of mesh cell in a certain direction,
   the corresponding parameters in the **Bound** have no practical meaning.

In addition, when using the fission matrix to solve the critical adjoint flux, it is necessary to write the
fission matrix of the same geometric mesh in the source convergence module.

Output counting results generation by generation
-------------------------------------------------------

.. code-block:: none

  Outputcyc [num]

The **Outputcyc**  option specifies to enable and set the number of generations of tally results
output in critical calculations, and [num] defines the number of generations the tally results are output each time.
If you need to perform statistical tests on the counting results output for each generation, you will also need
to turn on the statistical test switch in the Tally input option.

Criticality calculation module input example
--------------------------------------------

.. code-block:: none

  CRITICALITY
  PowerIter Population = 5000 30 100 // keff0 = 1.0
  InitSrc point = 0.0 0.0 0.0
                  0.5 0.5 0.0
                 -0.5 -0.5 0.0
  RNG type = 3 seed = 12345 stride = 10000
  ParallelBank 1

In the critical calculation module above, the **PowerIter** input card specifies that the number of particles
per generation is 5000, 30 generations are skipped, and a total of 100 generations are simulated; the
initial effective proliferation coefficient is the default value, that is, 1.0. **InitSrc** specifies
that the type of the initial source is a point source, and the positions are (0, 0, 0), (0.5, 0.5, 0) and
(-0.5, -0.5, 0). Fission sources will be randomly generated at these three positions. **RNG** specifies
that the random number type is a 64-bit mixed linear congruential random number generator, the initial
random number seed is 12345, and the random number length assigned to each particle is 10000. **ParallelBank**
indicates that the peer-to-peer mode is used to collect and distribute fission sources in parallel computing.
