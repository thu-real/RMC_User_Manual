.. _section_eng_serialize_restart_calc:

Serialized Restart Calculation
==============================

RMC supports using serialization functionality to implement restart calculations. This feature can be used to restart calculations after RMC has stopped computing.

Serialized Restart Calculation Module Input Options
-------------------------------------------------------------

.. code-block:: none

    SERIALIZE
    RESTARTCRITICALITY  Write =<flag>  Cycles=<cycles_vector_group>

Among them,

-  **SERIALIZE** \  is the keyword for the serialized restart calculation module;

-  **RESTARTCRITICALITY** \  is the keyword for using serialized restart calculation in criticality calculations;

-  **Write** \  specifies whether to output data files, 1 indicates output, 0 indicates no output, the default value is 0; When this option is 1, 
    the input value in the \ **Cycles** \  input option must be defined.

-  **Cycles** \  specifies the generations where all data is output in memory in binary format after the calculation is completed in criticality calculation mode;

For example, when performing a criticality calculation, add to the input card:

.. code-block:: none

    SERIALIZE
    RESTARTCRITICALITY  Write = 1 Cycles= 3 23 43

The program will output all data after the 3rd, 23rd, and 43rd generations of neutron simulation are completed.

The program will create a corresponding number of folders (3 in this example) in the directory where the input card file is located. The folder names will be 
"'CriSerializeFiles' + space + generation number". Data saved after different generations will be stored in different folders. The data files in the folders 
are in binary format, with one data file corresponding to an object of a class. The file name is the object name in the program code. The data files saved in 
criticality calculations are listed in the table below. Data files for the same object after different generations have the same name and are distinguished 
only by the folder they are in.

=========================  ====================================
File Name                  Description
=========================  ====================================
cCalMode                   Calculation mode
cFixedSource               Fixed source
cNeutronTransport          Neutron transport
cPhotonTransport           Photon transport
cElectronTransport         Electron transport
cAceData                   ACE cross-section data
cCriticality               Criticality
cMaterial                  Material
cGeometry                  Geometry
cParticleState             Particle information
cConvergence               Convergence
cTally                     Tally
cBurnup                    Burnup
ORNG                       Random number
cAdjoint                   Adjoint
cSampling                  Random sampling
OXSParaTableVec            Cross-section parameterization class
Output                     Output class
cellVector                 Class for fast hashing of cell expansion expressions
OParticleTracker           Class for unified control of particle event filtering and output in the program
OMeshInfo                  Mesh information
OWeightWindow              Weight window class
OStatus                    Class for recording RMC running status
=========================  ====================================

Data File Usage
-----------------------

To read in saved data files for continuation calculations, simply run the RMC program again. The program will automatically detect whether there are 
complete data files in the default save path, select and read the data with the most completed neutron generations to continue the subsequent calculations.

As seen in the previous example, if only the "CriSerializeFiles 3" and "CriSerializeFiles 23" folders exist and are complete, the program will read the files 
in "CriSerializeFiles 23" and start neutron generation simulation calculations directly from the 24th generation, and will still output all information after the 43rd generation.

Here, the \ **data completeness** \  means that the folder exists and contains files with corresponding file names, but the correctness of the information in the files is not checked. 
Hence, the user should not modify these automatically saved files to ensure the correctness of the restart calculation results.

