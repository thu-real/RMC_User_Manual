.. _section_eng_fixedsource:

Fixed Source Calculations
==========================

RMC supports fixed source calculation function. The fixed source function is used to calculate the changes
of neutron distribution in the system with space, energy and time when the external neutron source is known.
Fixed source calculation can be divided into steady-state calculation and time-dependent calculation. The current
RMC version only supports steady-state calculation function. At the same time, the RMC fixed source function
can use neutron cross-section data in ACE format with multiple groups and continuous energy. It should be noted
that steady-state fixed source calculation is only applicable to systems without fission nuclides or
subcritical breeding systems.

.. _section_eng_fixedsource_particle:

Total number of simulated particles
-----------------------------------

.. code-block:: none

    FixedSource
    PARTICLE population=<N>  fission=<fN fP> 



where,

-  **FixedSource** \ is the keyword for the fixed source module;

-  **population** \ specifies the initial number of particles used in the fixed source calculation,
   and N represents the initial number of particles;

-  **fission** \ specifies whether to turn off fission neutrons and photons. If fN is 0, it means turning off
   fission neutrons. If fN is 1, it means turning on fission neutrons. If fP is 0, it means turning off
   fission photons. If fP is 1, it means turning on fission photons.

.. important:: If the fission option is not input, fission neutrons and photons are turned on by default.


.. _section_eng_fixedsource_adjoint:

Adjoint Calculations
---------------------

Currently, RMC supports the use of adjoint calculations in fixed source mode to calculate the adjoint problem
of the forward problem, and can also calculate the adjoint flux. The basic logic of this function is to adjust
the state of the sampled collision particles and neutron collisions after the collision (i.e. after sampling
the free path). In essence, it uses multiple groups of forward cross sections to generate a set of adjoint
calculation cross sections, and uses the adjoint cross sections to sample the collision nuclides and adjust
the neutron emission state to update it to meet the logic of adjoint transport:

.. code-block:: none

    Adjoint [ADJOINTCALCULATION=<N> MaxAdjointEnergy=<maxNeuErg maxPhoErg>]

-  **Adjoint** \ is an input option related to adjoint calculation

-  **ADJOINTCALCULATION** \ specifies that the adjoint calculation with fixed source will be used in this calculation.
   The adjoint section used by the adjoint calculation will be generated inside the program. Setting this option to
   1 turns on the adjoint calculation with fixed source.

-  **MaxAdjointEnergy** \ is the upper limit of particle energy for fixed source multi-group adjoint
   calculation simulation. It must be used together with the ADJOINTCALCULATION input option. Two numbers need to be
   inputted in this option, which are the upper limits of the energy of the adjoint neutron and the
   adjoint photon, in MeV. The default values for both are 20MeV. When the adjoint particle energy
   exceeds this value, it will be killed.



.. _section_eng_fixedsource_rng:

Random Number Generator
-----------------------

RMC contains three different types of random number generators. For general users, it is recommended to use
the default random number generator type and parameters. Advanced users may need to use specific random number
parameters, for example, use different random number seeds for the same case to obtain independent calculation
results. RMC provides input options for custom random number generators:

.. code-block:: none

  RNG [Type = <type>] [Seed = <seed>] [Stride = <stride>]


where,

-  **RNG** \ is the keyword for the random number generator input option.

-  **Type** \ specifies the type of random number generator. \ **Type = 1** \ is a 48-bit multiplicative linear
   congruential random number generator, **Type =  2** \ (default) is a 64-bit multiplicative
   linear congruential random number generator, \ **Type = 3** \ is a 64-bit hybrid linear congruential
   random number generator, and \ **Type = 5** \ is a 128-bit random number generator.

-  **Seed** \ specifies the initialization seed of the random number generator, which can be any positive odd number.
   The default value is \ **Seed = 1** \.

-  **Stride** \ is used to set the length of the random number of each neutron history when parallel computing
   is performed. It is only recommended for advanced users. The default value is \ **Stride = 10000** \.

.. _section_eng_fixedsource_cutoff:

Truncation Conditions
---------------------

.. code-block:: none

  FixedSource
  CutOff [MaxLost=<maxLost>]
         [MinWeight=<minWeightN minWeightP>]
         [MaxWeight=<maxWeightP>]

where,

-  **CutOff** \ is the keyword for the truncation condition input card.

-  **MaxLost** \ specifies the maximum number of particles that the program allows to be lost. The default value is 10.

-  **MinWeight** \ specifies the lower limit of the weight in the transport process. **minWeightN** \ specifies the lower
   limit of the weight of neutrons, with the default value as 0.25, and **minWeightP** \ specifies the lower limit of the
   weight of photons, with the default value as 0.6. After the weight window variance reduction function is
   turned on, the input here is invalid

-  **MaxWeightP** \ specifies the upper limit of the weight of photons in the transport process. The default value is 1.4.

Dynamic Load Balancing for Fixed-Source Calculations (Enterprise Edition Only)
------------------------------------------------------------------------------------

When there are significant differences in the computational capabilities of various cores in a running machine,
pre-allocating loads may result in extended waiting times for slower processes (on slower cores) completing their
respective allocated tasks in the later stages of computation. This leads to wasted computational time on other cores
and hence lengthens the overall computation time. As such, it is advisable to enable dynamic load balancing to
implement the principle of "those who are capable to do more" by distributing the workload amongst the cores of
the running machine at an equitable level.

.. code-block:: none

  FixedSource
  LOAD_BALANCE [Method=<method>]
               [Interval=<interval>]

where,

-  **Method** \ is the keyword of the dynamic load balancing method. The current permissible values are 0 or 1.
   0 means dynamic load balancing is not used, 1 means the dynamic load balancing method of time aggregation is used,
   and the default value is 0, which means turning off dynamic load balancing.

-  **Interval** \ specifies the time check interval for the time merge dynamic load balancing method. It is an integer
   in seconds. The default value is 60 seconds.

Note:

1. The actual number of simulated particles of the time-merged dynamic load balancing method is generally
greater than the number of particles set in the input card. If there are strict requirements on the
number of simulated particles, please do not use this dynamic load balancing method, or use statistical
principles to adjust the results afterwards.

2. Generally speaking, the larger the Interval parameter is, the more particles will be simulated,
but a short interval (such as once per second) may lead to frequent merging and statistics, thus slowing
down the calculation. Under normal circumstances, the default value of once per minute is sufficient for
merging and checking. Only when the number of particles actually simulated is quite small do you need to
reduce the interval value.

3. After enabling time-merged dynamic load balancing, the number of particles calculated each time may
be different, so reproducibility cannot be guaranteed. However, if there are enough particles,
the result will be within the allowable error range.

4. This function only supports RMC programs with MPI enabled. In the serial version of RMC, this function
will not have any effect. When using single-process calculation in an RMC program with MPI enabled and
turning on this function, this function will have an effect, but it is generally a negative effect, that is,
the calculation time increases and the number of calculated particles increases.

External Source Subroutines for Fixed-Source Calculations
---------------------------------------------------------

When a description of a general source requires a higher degree of flexibility in terms of its definition, an external source subroutine can be used.
The input card for this part is:

.. code-block:: none

  SOURCESUB [SOURCE = <source>] [PTMOD = <PTMod>]


where,

-  **SOURCESUB** \ is the keyword indicating the use of an external subroutine. In this case, there is no need to
   define the **EXTERNALSOURCE** \ module input card. Instead, a python script is provided as an external
   subroutine. The user customizes this subroutine and provides the necessary particle information;

-  **SOURCE** \ is the keyword of the file name of the external subroutine. It must be provided when using
   **SOURCESUB** \. The input value does not have the suffix .py and is case-sensitive;

-  **PTMOD** \ is the keyword of the point tally correction subroutine. If you use the point tally at the same
   time as the external subroutine, you must provide this subroutine. If you do not use it, you do not need to write
   it. The input value does not have the suffix .py and is case-sensitive.

The subroutines should be placed in the same folder as the input card inp file. For detailed usage of the
two subroutines, refer to :ref:`Source Description <section_eng_external_source>`. The following is an input example of this card:

.. code-block:: none

  SOURCESUB Source = source PTMod = ptmod

This function does not currently support Windows systems and can only be used in Linux systems.

This feature requires cmake version 3.12 or above; the dev version of python is also required, which can be
installed using the command sudo apt-get libpython3-dev.


.. _section_eng_fixedsource_example:

Fixed source module input example
---------------------------------

The fixed source releases 10,000 source neutrons.

.. code-block:: c

    UNIVERSE 0
    cell 1   -1      mat = 1           // sphere inside
    cell 2   1 & -2  mat = 1           // sphere middle
    cell 3   2 & -3  mat = 2           // sphere outside
    cell 4   3       mat = 0  void = 1

    SURFACE
    surf  1  so  10
    surf  2  so  20
    surf  3  so  30

    MATERIAL
    mat 1  -10.045   // Fuel
        92235.71c   6.89220E-03
        92238.71c   2.17104E-02
        8016.71c    4.48178E-02
    mat 2  -0.9     // Water
        1001.71c   2.0
        8016.71c   1.0
    sab 2  HH2O.71t

    FixedSource
    particle population = 10000

    EXTERNALSOURCE
    Source 1 particle = 1 energy = 0.1 sphere = 0 0 0 0 5
    
    Tally
    CellTally  5  type = 1  cell = 1 2  time=0 5.0e-8  1.0e-7  5.0e-7 1.0e-6
    CellTally  6  type = 2  cell = 1 2  energy = 0 6.25E-7 20 time=0 5.0e-8  1.0e-7  5.0e-7 1.0e-6

