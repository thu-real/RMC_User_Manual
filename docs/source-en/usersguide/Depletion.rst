.. _section_eng_pointburnup:

Depletion Calculations
========================

.. _section_eng_pointburnup_intro:

Overview of Depletion Calculation
-----------------------------------

The depletion module of RMC is based on the independently developed modular program DEPTH, which is suitable for complex burnup systems. 
DEPTH has the following characteristics in terms of database, algorithms, and functionality:

1. In terms of database, it organically integrates the burnup databases of ORIGEN-2 and ORIGEN-S, providing comprehensive burnup chain information.

2. In terms of algorithms, DEPTH includes four solvers: the Linear Sub-chain Method (TTA), Chebyshev Rational Approximation Method (CRAM), 
   Quadrature-based Rational Approximation Method (QRAM), and Laguerre Polynomial Approximation Method (LPAM). Additionally, in terms of program implementation, 
   it fully utilizes the sparsity of the burnup matrix, adopting a Gaussian elimination method based on row and column mixed compression storage of sparse matrices, 
   greatly optimizing the speed of burnup calculations.

3. In terms of functionality, it supports three burnup modes: decay, constant flux, and constant power. It can calculate physical quantities such as nuclear densities, 
   decay heat, radioactivity, and reaction rates. Furthermore, it supports the calculation of instantaneous values of certain physical quantities and their time-integrated 
   values within the burnup step length.

.. _section_eng_pointburnup_cards:

Depletion Module Input Cards
------------------------------

The input cards for the depletion module include:

.. code-block:: none

   Depletion
   Convertlib <lib_type> <xs_data> <yield_data> <main_lib>
   Decay <time>
   Flux <flux> <time>
   Power <power> <time>
   Density <nuc_id> <nuc_den>
   Solver <solver_type>
   TTA <cutofffrac>
   QRAM <qrad_n>
   LPAM <Laguerre_a> <Laguerre_tao> <Laguerre_N>
   Print <flag>
   Radioactivity <flag>
   AbsorpRate <flag>
   FissionRate <flag>
   DecayHeat <flag>
   FluxPower <power_flag> <flux_flag>
   Energy <neu_erg>
   GAMMASPECTRUM <gammaspectrum_flag>
   DecayEnergy <e_1 e_2 ... e_n>
   GammaLibPath <library_path_of_gammaspectrum>
   

Among them,

-  **Depletion** \  is the keyword for the depletion calculation module.

-  **Convertlib** \  is the database conversion option.  \ **Convertlib = 1** \  indicates the \ **ORIGIN-S** \  
   database, and \ **Convertlib = 1** \  indicates \ **ORIGIN-2** \  database.  Subsequently, the decay database, 
   yield database, and burnup database names used by \ **RMC** \   are specified for the \ **ORIGIN** \  database. 
   Given that RMC's depletion function uses the independently developed DepthMainLib burnup database, it is not 
   used by default unless there are special requirements.

-  **Decay/Flux/Power** \  are the modes for depletion calculation, representing decay, constant flux, and constant power modes, respectively.

   -  **Decay** \  indicates performing decay calculations, with a default power of 0, 
      so there is no need to input power or flux. Simply input the decay time.

   -  **Flux** \  indicates performing constant flux calculations. The user needs to 
      input the target neutron flux, followed by the depletion calculation time. 
      For example, 1e+24 10d * 10.

   -  **Power** \  indicates performing constant power calculations. The user needs 
      to input the target power (in watts), followed by the depletion calculation time. 
      For example, 1000000 10d * 10.

   Note that users can specify the units of the depletion time input, which can be \ **y, d, h, m, s** \ 
   representing years, days, hours, minutes, and seconds, respectively. An example of time input: 
   \ **4y*10** \  . This means performing calculations in 10 time steps, each time step being 4 years. 
   Note: Different time units such as \ **1y2d3h** \   are not supported, nor are different time steps such as 
   \ **1y*10 5d*5** \ . Additionally, depletion calculations currently do not support variable power or variable 
   flux depletion calculations.

-  **Density** \  provides information on the nuclides to be calculated, including the nuclide ID and the nuclide density. 
   \ **nuc_den > 0** \  indicates atomic density, in units of 10\ :sup:`24`\  atoms/cm\ :sup:`3`\ ;\ **nuc_den < 0** \   indicates mass density, 
   in units of g/cm\ :sup:`3`\ . Note: Please use the ID format from the burnup database for the nuclide ID, such as 
   \ **10010 922350 922351** \  , where the last digit indicates whether it is in an excited state.

-  **Solver** \  specifies the method of solving the depletion equation. \ **Solver = 1** \  indicates Transmutation Trajectory Analysis(TTA), 
   \ **Solver = 2** \  (default) indicates the Chebyshev rational approximation method(CRAM), 
   \ **Solver = 3** \  indicates the Quadrature Rational Approximation Method(QRAM), 
   \ **Solver = 4** \  indicates the Laguerre polynomial approximation method(LPAM). For general users, it is recommended to use the default parameters.

   -  **TTA** \  is used only when \ **Solver = 1** \  indicating the stage error of the \ **TTA** \  method. 

   -  **QRAM** \  is used only when \ **Solver = 3** \  indicating the approximation order of the \ **QRAM** \  method.

   -  **LPAM** \  is used only when \ **Solver = 4** \  indicating the initial parameters of the \ **LPAM** \   method. 

-  **Print** \   is the output option for depletion. \ **Print = 0** \  outputs the physical quantities at the last burnup step, 
   \ **Print = 1** \  outputs the physical quantities at all burnup steps.

   -  **Radioactivity** \  indicates radioactivity, in units of \ **Curi** \  ,\ **Radioactivity = 
      0** \  (default) does not output radioactivity, \ **Radioactivity = 1** \  outputs instantaneous concentration, and
      \ **Radioactivity = 2** \  outputs the integrated concentration over the burnup step.

   -  **AbsorpRate** \  indicates neutron absorption rate, in units of \ **n/s** \  ,\ **AbsorpRate = 
      0** \  (default) does not output neutron absorption rate, \ **AbsorpRate = 1** \  outputs instantaneous concentration, and 
      \ **AbsorpRate = 2** \  outputs the integrated concentration over the burnup step.

   -  **FissionRate** \  indicates fission rate, in units of \ **fission/s** \  ,\ **FissionRate = 
      0** \  (default) does not output neutron fission rate, \ **FissionRate = 1** \  outputs instantaneous concentration, and 
      \ **FissionRate = 2** \  outputs the integrated concentration over the burnup step.

   -  **DecayHeat** \  indicates decay heat, in units of \ **Watt** \  ,\ **DecayHeat = 0** \   (default) 
      does not output fission rate, \ **DecayHeat = 1** \  outputs instantaneous concentration, and  \ **DecayHeat 
      = 2** \  outputs the integrated concentration over the burnup step.

   Note: Instantaneous concentration refers to the concentration at the end of the depletion calculation, 
   while integrated concentration is the concentration integrated over the burnup step.

-  **FluxPower** \  indicates whether to use the average value of flux or power. \ **flux/power_flag = 0** \  
   (default) indicates using instantaneous values, \ **flux/power_flag = 1** \  indicates using average values.

-  **Energy** \   indicates neutron energy, in units of \ **eV** \  

-  **GAMMASPECTRUM**\ indicates the decay photon spectrum calculation function.\ **GAMMASPECTRUM = 0**\ (default value) means that the decay photon 
   spectrum calculation function is not used, while \ **GAMMASPECTRUM = 1**\ indicates that the decay photon spectrum calculation function is enabled.

   - **DecayEnergy**\ refers to discrete energies, measured in MeV, which are defined by the user.

     .. caution::The user-defined discrete energies must be an increasing sequence; otherwise, the calculation results will be erroneous.
   
   - **GammaLibPath**\ specifies the absolute path to the database related to decay photon spectra, typically located in the neutron_hdf5 folder under RMC_DATA. 
     When using the decay photon spectrum calculation function in point burnup mode, this must be specified, e.g., /home/workspace/RMC_DATA/neutron_hdf5.


It should be noted that calculation modes, solver parameters, and output options can be specified as needed and do not need to be included if not required.

.. _section_eng_pointburnup_example:

Depletion Module Input Example
--------------------------------

Neptunium \ **Np** \  Depletion Calculation Example
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

In the Neptunium \ **Np** \  depletion calculation example, the decay mode is used, with a burnup duration of 10\ :sup:`6`\ years. The initial nuclide density is
1*10\ :sup:`24`\ atoms/cm \ :sup:`3`\ ,and the \ **CRAM** \   method is used for solving.

.. code-block:: c
  :caption: Neptunium \ **Np** \  depletion input
  :name: Np_pointburnup_input_eng

  /// Np point burnup input file  ////
  Depletion
  Decay        100000y * 10
  Density   932370  1         
  SOLVER 2              
  print  0                
  Radioactivity   1        
  AbsorpRate      1        
  FissionRate     1       
  DecayHeat       1    

