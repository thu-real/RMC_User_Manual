.. _section_eng_input_summary:

Input Overview
===============

Input Modules
---------------

RMC's input file is divided into modules. The names and corresponding functions of each module are as follows:

   -  **SURFACE** \  module: Defines surface types and surface equations.

   -  **UNIVERSE** \   module: Describes a complete geometric space. RMC uses a hierarchical space-based geometry description, 
      and multiple UNIVERSE modules may exist in the input file.

   -  **MATERIAL** \   module: Defines material composition.

   -  **CRITICALITY** \   module: Defines criticality calculation parameters, including particle numbers, initial sources, etc.

   -  **TALLY** \  module: Defines tallies, including flux, power, reaction rates, etc.

   -  **CONVERGENCE** \  module: Defines source convergence diagnostics and acceleration parameters.

   -  **BURNUP** \  module: Defines burnup calculation parameters, including burnup cells, power, time steps, etc.

   -  **PRINT** \  module: Defines output print content.

   -  **PLOT** \  module: Defines plotting parameters.

   -  **FIXEDSOURCE** \  module: Defines fixed source calculation parameters, mainly including the number of initial source particles to be simulated.

   -  **EXTERNALSOURCE** \   module: Defines external source particle information, including particle type, position, direction, and energy distribution.

   -  **PHYSICS** \  module: Defines physical parameters for neutron-photon-electron coupled transport calculations.

   -  **GROUPCONSTANT** \  module: Defines group constants and cross-section parameterization calculation parameters.

   -  **RESTARTBINARY** \   module: Defines output parameters for checkpoint continuation calculation files.

   -  **BINARYOUT** \  module: Defines the output parameters for continuation surface source calculation files.

   -  **PTRAC** \  module: Defines particle event tracking parameters.

   -  **WEIGHTWINDOW** \  module: Defines weight window parameters.

   -  **MCNPWEIGHTWINDOW** \  module: Defines MCNP format weight window parameters.

   -  **KINETICS** \ module: Defines stochastic neutron kinetics calculation parameters.

   -  **CONTROL** \ module: Defines control parameters for some of RMC's running characteristics, such as Hash function selection parameters.

   -  **REFUELLING** \ module: Defines refueling parameters.

   -  **INCLUDE** \  module: Used for file inclusion.

   -  **SERIALIZE** \  module: Defines output parameters for serialized restart data files.

Input Format
------------

The following points should be noted for the format of RMC input files:

1. Each module is identified by its corresponding keyword, and modules are separated by blank lines. For example:

    .. code-block:: none

      Universe 0

      ……

      Universe 1

      ……

      Surface

      ……

      Material

      ……

      Criticality

      ……


2. Input cards are written in the top row of each module, and options within input cards are separated by spaces. If an input card is not 
   completed in one line, it can be continued on the next line with a space. For example:

     .. code-block:: none

       CellTally 2 type = 1 filter = 1 0 1 energy = 0 6.25E-7 20.0
                   cell = 2 > 0 > 1:289


3. The comment symbol uses "//" (C++ style).

4. RMC input files are case-insensitive.

5. On Windows system, it is not recommended to use txt format text files as input files. It is recommended to use UltraEdit to convert all input files to Dos format.

