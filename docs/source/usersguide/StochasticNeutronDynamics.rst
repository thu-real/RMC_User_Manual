.. _section_eng_stochastic_neutron_dynamics:

Stochastic Neutron Dynamics (Enterprise Version Only)
=====================================================

RMC supports stochastic neutron dynamics calculation and simulation functions such as neutron static system
ignition probability, dynamic system survival probability and neutron pulse initiation time, and lastly, RMC also
supports the direct simulation method for neutron dynamics calculation function.

When simulating stochastic neutron dynamics, the neutron chain is used as a unit. According to the defined time
step size, the evolution characteristics of the neutron chain over time are simulated, the standard of
neutron chain self-sustaining is set, and the ignition probability of statistical neutrons or the pulse
initiation time of the supercritical system is calculated.

Stochastic Neutron Dynamics Input Option
----------------------------------------

.. code-block:: none

     KINETICS
     Particle  FissionChain=<fission_chain_number>  TimeStep=<time_step>
               StochasticMode=<calculation_mode_option>
               [MaxPopulation=<maximum_population>]  [Batch=<batch_number>]
               [Lambda=<sponta_fission_rate>]  [Nu=<average_neutron_number>] 
               [TerminateTime=<terminate_time>]  [TerminateFisRate=Terminate_fission_rate]
               [ExpFisRate=<exponential_fission_rate>]  [TargetFisRate=<target_fission_rate>]
               [InitSourceNum=<initial_neutron_number>] [PrecursorMode=<precursor_mode_option>]
               [DecayTimeStep=<decay_timestep>] [CaptureMode=<capture_mode_option>]
               [UseComb=<use_comb_method_option>] [ControlNum=<comb_control_number>]
               [FissMultiplicity=<fission_multiplicity>] [SpontaFiss=<spontaneous_fission>]
               [InitCriSrc=<initial_critical_source>] [GeoMatTimeStep=<geo_mat_time_step>]
               [PosStartTime=<start_time>] [PosEndTime=<end_time>] 
     TimeStep  Time=<t0 t1 t2 ... tn>  Step=<s1 s2 ... sn>
     RNG       [Type = <type>] [Seed = <seed>] [Stride = <stride>]
     FissMult  zaid=<nuclide_ID>  method=<number> data=<number> shift=<number>


where,

-  **KINETICS** \ is the keyword for random neutron dynamics. \ **Particle** \ defines the parameters related to the neutron
   chain, \ **TimeStep** \ defines the parameters related to the variable time step, \ **RNG** \ defines the random number.
   The usage method of \ **RNG** \ is the same as the random number definition input option in the criticality
   calculation module, hence it will not be repeated here. \ **FissMult** \ defines the detailed neutron fission
   multiplicity parameter settings, which is the same as the usage method of the FissMult card in the fixed
   source calculation mode, so it will not be repeated here. The following introduces the usage of the \ **Particle** \ and
   \ **TimeStep** \ input options.

Particle Input Option
~~~~~~~~~~~~~~~~~~~~~~

The Particle input option specifies the relevant parameters of the neutron chain in
the stochastic neutron dynamics simulation.

-  **FissionChain** \ specifies the number of simulated neutron chains, which must be a positive integer.
-  **TimeStep** \ is the time step interval during the subchain simulation, in seconds.
-  **StochasticMode** \ is the random neutron dynamics calculation mode option. 0 represents the pulse initiation
   time calculation or direct simulation neutron dynamics calculation mode, 1 indicates the POI calculation mode,
   2 indicates that the neutron library tracking method is used to calculate POS, 3 indicates that the single neutron
   tracking method is used to calculate POS, and the default value is 0.
-  **MaxPopulation** \ specifies the maximum number of neutrons in the neutron chain simulation. When the number of
   prompt neutrons in the neutron chain exceeds this number, the neutron chain is considered self-sustaining and
   the simulation of the neutron chain is stopped.
-  **Batch** \ defines the number of groups. This card is used when using group statistics to calculate the
   uncertainty of the ignition probability. By default, there is no grouping, that is, the number of groups is 1.
-  **Lambda** \ defines the fission intensity of the spontaneous fission source, in units of /s.
-  **Nu** \ defines the average fission neutron number of a spontaneous fission source.
-  **TerminateTime** \ defines the termination time of the subchain simulation, in seconds.
-  **TerminateFisRate** \ defines the fission reaction rate at which the neutron chain simulation terminates,
   in units of /s.
-  **ExpFisRate** \ defines the fission reaction rate at which exponential growth begins during neutron chain simulation.
   If this fission rate is exceeded, the neutron chain is considered to have entered a significant exponential
   growth phase.
-  **TargetFisRate** \ defines the extrapolated target fission reaction rate during neutron chain simulation. In a static
   supercritical system, the time when the neutron chain fission rate reaches \ **TargetFisRate** \ can be extrapolated
   based on the defined \ **ExpFisRate** \ and \ **TargetFisRate** \ exponents.
-  **InitSourceNum** \ defines the initial neutron source number, used in the direct simulation neutron dynamics
   calculation mode.
-  **PrecursorMode** \ defines the delayed neutron precursor decay mode. 0 is direct simulation of the precursor
   (commonly used in pulse initiation time calculation mode), 1 is forced decay of the precursor (commonly used in
   direct simulation neutron dynamics calculation mode), and the default is 0.
-  **DecayTimeStep** \ is the time step for the forced decay of the delayed neutron precursor nucleus
   (i.e. forced decay once every this time step). The default value is 1ms and is only valid in the forced decay
   mode of the precursor nucleus.
-  **CaptureMode** \ handles two modes of radiation capture reaction, 0 is direct simulation absorption and
   killing particles, 1 is latent capture, with the default value being 0.
-  **UseComb** \ indicates whether to enable the comb method, with the value of 0 meaning off, 1 meaning on, and the
   default value being 0.
-  **ControlNum** \ is the number of particles adjusted by the comb method. It is only valid after the comb method
   is turned on. The default value is 10000.
-  **FissMultiplicity** \ defines if fission multiplicity is enabled, with 0 indicating off, 1 indicating on, and
   the default is 1.
-  **SpontaFiss** \ defines if spontaneous fission is enabled, with 0 indicating off, 1 indicating on, and
   the default is 1.
-  **InitCriSrc** \ defines if there is an initial critical source (firstly, calculate the source using quasi-static S
   mode, and generate a source file with the suffix ".KineticsInitSource"), where the value of 0 means no initial
   critical source is used, the value of 1 means an initial critical source is used, with the default value being 0.
   If it exists, the initial source distribution is determined by the given file, which is often used in
   the direct simulation neutron dynamics calculation mode.
-  **GeoMatTimeStep** \ defines the time step for geometry and material changes (applied in POS).
-  **PosStartTime** \ defines the start time of POS calculation.
-  **PosEndTime** \ defines the end time of POS calculation.

TimeStep Input Option
~~~~~~~~~~~~~~~~~~~~~~

The TimeStep input option specifies the parameters related to the dynamic time step in stochastic neutron
dynamics simulations for dynamic systems (geometry or materials that vary with time).

-  **Time** \ specifies the time interval boundary in seconds.
-  **TimeStep** \ represents the time step length within each time interval. The number is one less than the
   number of time interval boundaries. The unit used here is seconds.


Stochastic Neutron Dynamics Module Input Example
------------------------------------------------

-  Neutron Static System Ignition Probability Calculation Example

.. code-block:: c

    KINETICS
    Particle  StochasticMode=1 FissionChain=1000 TimeStep=1e-9  MaxPopulation=1000 Batch=10


This example defines the calculation parameters for the ignition probability of a neutron static system, with the
calculation mode used here as POI. 1000 neutron chains are simulated and divided into 10 groups for
simulation and statistics. The simulation time step size is 1 nanosecond (ns). The upper limit of the
number of prompt neutrons in each neutron chain is 1000.

-  Neutron Dynamic System Survival Probability (Neutron Bank Tracking Method) Calculation Example

.. code-block:: c

    KINETICS
    particle  FissionChain = 100000 TimeStep=10e-8  MaxPopulation=100000000 batch=20 PosStartTime=990e-8 PosEndTime=1000e-8 ControlNum=100000 GeoMatTimeStep=10e-8 StochasticMode=2


This example defines the calculation parameters for calculating the survival probability of a dynamic system
using the neutron bank tracking method. The calculation mode is POS, 100,000 neutron chains are simulated,
and they are divided into 20 groups for simulation and statistics. The simulation time step size is 10 sh,
the upper limit of the number of prompt neutrons in each neutron chain is 100,000,000, the start time of POS
calculation is 990 sh, the end time is 1000 sh, the comb method threshold is 100,000 (the comb method is
enabled when the number of particles exceeds this value, and the comb method is disabled when it is lower
than this value), and the geometric material change time step is 10 sh.

-  Neutron Dynamic System Survival Probability (Single Neutron Tracking Method) Calculation Example

.. code-block:: c

    KINETICS
    particle  FissionChain = 100000 TimeStep=10e-8  MaxPopulation=100000000 batch=20 PosStartTime=990e-8 PosEndTime=1000e-8 GeoMatTimeStep=10e-8 StochasticMode=3

This example defines the calculation parameters for calculating the survival probability of a dynamic
system using the single neutron tracking method. The calculation mode is POS, 100,000 neutron chains are
simulated, and they are divided into 20 groups for simulation and statistics. The simulation time step size
is 10 sh, and the upper limit of the number of prompt neutrons in each neutron chain is 100,000,000. The start
time of POS calculation is 990sh, the end time is 1000sh, and the time step of geometric material change is 10sh.

-  Neutron Pulse Initiation Time Calculation Example

.. code-block:: c

    KINETICS
    Particle  StochasticMode=0 fissionchain=2  TimeStep=0.1  MaxPopulation=100000  lambda=200  
              Nu=1  TerminateTime=300  TerminateFisRate=2.0e9  ExpFisRate=1e9
              TargetFisRate=2.7e11
    TimeStep  Time=0 10  300  Step=0.1 0.5

This example defines the calculation parameters of the neutron pulse initiation time (all unset
parameters are set to their default values). The calculation mode is pulse initiation time
calculation. Two neutron chains are simulated with a time step of 0.1s. The maximum number of prompt neutrons
for each neutron chain is 100,000. If it exceeds 100,000, the neutron pulse initiation is considered successful.
The spontaneous fission source strength is 200 times/s. The average number of fission neutrons released by
each spontaneous fission is 1. The cutoff time for each neutron chain simulation is 300s. If it exceeds 300s,
the simulation of the neutron chain is terminated. The cutoff fission rate of each neutron chain is 2.0e9/s. If it
exceeds this fission rate, the simulation of the neutron chain is automatically stopped. The fission reaction
rate at which each neutron chain starts to grow exponentially is 1e9/s. If it exceeds this fission rate, the
neutron chain is considered to have entered a significant exponential growth stage. The extrapolated target
fission reaction rate during neutron chain simulation is 2.7e11/s. This example also defines a dynamic time step.
At this time, the time step defined in the Particle card is invalid. Between 0 and 10s, the time step is 0.1s,
and between 10s and 300s, the time step is 0.5s.


-  Example 1 of using the Direct Simulation Method for Neutron Dynamics Calculation

.. code-block:: c

    Kinetics
    particle  StochasticMode=0 UseComb=1 ControlNum=1000 InitSourceNum=1000 PrecursorMode=1  CaptureMode=1 
              SpontaFiss=0 FissMultiplicity=0  FissionChain =10  TimeStep=0.000001 
              MaxPopulation=1e12 TerminateTime=0.00001 


This example defines the calculation parameters of direct simulation neutron dynamics. The
calculation mode is direct simulation neutron dynamics. The comb method is used. The number of particles
controlled by the comb method is 1000, the number of initial source neutrons is 1000, the delayed neutron
decay mode is forced decay, the radiation capture mode is latent capture, spontaneous fission is turned off,
fission multiplicity is turned off, 10 neutron chains are simulated, which is equivalent to dividing them
into 10 groups for simulation and statistics. The time step size during simulation is 0.000001s, the upper
limit of the number of neutrons in each neutron chain is 1e12, and the simulation end time is set to 0.00001s.

-  Example 2 of using the Direct Simulation Method for Neutron Dynamics Calculation

.. code-block:: c

    Kinetics
    particle  StochasticMode=0 UseComb=1 ControlNum=100 InitCriSrc=1 PrecursorMode=1  CaptureMode=0 
              SpontaFiss=0 FissMultiplicity=0 FissionChain = 4 TimeStep=1e-7 MaxPopulation=1e12 TerminateTime=1e-6   

    Tally
    CellTally 1 type = 1 cell = 1 time =0 1.0e-07 2.0e-07 3.0e-07 4.0e-07 5.0e-07 6.0e-07 7.0e-07 8.0e-07 9.0e-07 1.0e-06 
    CellTally 2 type = 2 cell = 1 time =0 1.0e-07 2.0e-07 3.0e-07 4.0e-07 5.0e-07 6.0e-07 7.0e-07 8.0e-07 9.0e-07 1.0e-06 
    CellTally 3 type = 3 cell = 1 time =0 1.0e-07 2.0e-07 3.0e-07 4.0e-07 5.0e-07 6.0e-07 7.0e-07 8.0e-07 9.0e-07 1.0e-06 


This example defines the calculation parameters of direct simulation neutron dynamics. The calculation mode is the
direct simulation method for neutron dynamics. The comb method is used. The number of particles controlled
by the comb method is 100. The initial critical source distribution is adopted (a source file with the
suffix ".KineticsInitSource" is required and to be placed in the same directory as the executable file). The
delayed neutron decay mode is forced decay, the radiation capture mode is direct simulation absorption,
spontaneous fission is turned off, fission multiplicity is turned off, and 4 neutron chains are simulated,
which is equivalent to dividing into 4 groups for simulation and statistics. The time step size during simulation
is 1e-7s, the upper limit of the number of neutrons in each neutron chain is 1e12, and the simulation
termination time is set to 1e-6s. Three counters are set for flux, power, and fission reaction rate, and
time binning is set.
