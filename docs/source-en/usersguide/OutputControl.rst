.. _section_eng_output:

Output Control
==============

The RMC output control module is used to customize the output content. Especially in large-scale burnup calculations, 
which may generate a large amount of output information, the output control module can effectively reduce the size of output files.

Output Control Module Input Options
------------------------------------------

The input options for the output control module is:

.. code-block:: none

  PRINT
  Mat <flag>
  Keff <flag>
  Source <flag>
  CellTally <flag>
  SURFACETALLY <flag>
  POINTTALLY <flag>
  MeshTally <flag>
  CsTally <flag>
  CYCTALLY <flag>
  Inpfile <flag>
  FissionSource <flag>
  Material2HDF5 <flag>
  BurnupCorrector <flag>


Among them,

-  **PRINT** \  is the keyword for the output control module.

-  Input options such as **Mat** \  ,\ **Keff** \  etc., specify whether to output related content(refer to :numref:`outputcontrol_table_eng`). \ **flag = 0** \
   indicates not to output the specified content, input options including **Keff**, **Source**, **CycTally**, **Inpfile** \ **flag = 1** \  indicates to output the specified content,
   input options including **Mat**, **CellTally**, **SurfTally**, **PointTally**, **MeshTally**, **CsTally**  \ **flag = n** \   indicates output to the nth decimal place

-  **FissionSource**\   is used to control the output of the converged (final generation) fission source information, including the positions, directions, and energy information of all 
   sources, to the file ``inp.Source.h5``.

.. table:: Input options for the output file control module
  :name: outputcontrol_table_eng

  +----------------------+-------------------------------------------------+----------------------------------------+
  | Input options        | Output Content                                  | Default Option                         |
  +======================+=================================================+========================================+
  | **Mat**              | Nuclide density list for all materials          | flag = 5, output to 5th decimal places |
  +----------------------+-------------------------------------------------+----------------------------------------+
  | **Keff**             | Keff for each generation                        | flag = 1, output                       |
  +----------------------+-------------------------------------------------+----------------------------------------+
  | **Source**           | Fission source information for each generation  | flag = 0, do not output                |
  +----------------------+-------------------------------------------------+----------------------------------------+
  | **CellTally**        | Results of cell tallies                         | flag = 4, output to 4th decimal places |
  +----------------------+-------------------------------------------------+----------------------------------------+
  | **SurfTally**        | Results of surface tallies                      | flag = 4, output to 4th decimal places |
  +----------------------+-------------------------------------------------+----------------------------------------+
  | **PointTally**       | Results of point tallies                        | flag = 4, output to 4th decimal places |
  +----------------------+-------------------------------------------------+----------------------------------------+
  | **MeshTally**        | Results of mesh tallies                         | flag = 4, output to 4th decimal places |
  +----------------------+-------------------------------------------------+----------------------------------------+
  | **CsTally**          | Results of cross-section tallies, including     | flag = 4, output to 4th decimal places |
  |                      | one-group cross-section during burnup           |                                        |
  |                      | calculations.                                   |                                        |
  +----------------------+-------------------------------------------------+----------------------------------------+
  | **CycTally**         | Tallies for each generation                     | flag = 0, do not output                |
  +----------------------+-------------------------------------------------+----------------------------------------+
  | **Inpfile**          | Continuation file input option                  | flag = 0, do not output                |
  +----------------------+-------------------------------------------------+----------------------------------------+
  | **Material2HDF5**    | Outputs material information to an HDF5 file    | flag = 0, do not output                |
  +----------------------+-------------------------------------------------+----------------------------------------+
  | **FissionSource**    | Converged fission source information            | flag = 0, do not output                |
  +----------------------+-------------------------------------------------+----------------------------------------+
  | **BurnupCorrector**  | Calculation results for burnup correction steps | flag = 1, output                       |
  +----------------------+-------------------------------------------------+----------------------------------------+


.. note:: It is important to note that Material2HDF5 is only effective when the user chooses to enable ``Inpfile``. In this case, the program will output material information 
  (including nuclear density, nuclide composition, etc.) to the ``material.h5`` file instead of a text file. Compared to previous text file outputs, the materials output to the 
  HDF5 file are easier for the program to read, which can effectively reduce the input card reading time in large-scale nuclear thermal coupling calculations. However, the 
  readability of its content is poorer. Notably, to maintain the readability of materials when outputting to an HDF5 file, the RMC Python module includes the ``Materials.from_hdf5`` 
  function. Users can use this function to read the corresponding HDF5 file and convert it into a text file, as shown below:

    .. code-block:: python

        from RMC.model.input.Material import Materials
        materials = Materials.from_hdf5('material.h5')
        with open('material.txt', 'w') as f:
            f.write(str(materials))

.. caution:: ``Material2HDF5`` option is recommended to be enabled only during large-scale nuclear thermal coupling calculations to reduce the time taken by the program to read material 
  files and accelerate initialization.

.. caution:: ``BurnupCorrector`` option is used to control the output of calculation results for correction steps when the estimated correction method is enabled in burnup calculations. 
  This includes results such as effective multiplication factors and counting statistics for correction steps. It is important to note that in nuclear thermal coupling calculations, 
  due to the existing framework where burnup calculations are split into single-step calculations, the counting results for correction steps will overwrite those for estimated steps 
  (primarily in the power counting file MeshTally1.h5) when the estimated correction method is enabled. However, what is actually needed in coupled calculations are the counting results 
  from the estimated steps. Therefore, it is recommended to set ``BurnupCorrector 0`` in the PRINT options during nuclear thermal coupling calculations.


.. _section_eng_output_example:

Output Control Module Input Example
--------------------------------------------

For burnup calculations with a large number of burnup zones, it is recommended to use the following input cards to 
suppress the output of material and one-group cross-section information to avoid generating large data files.

.. code-block:: c

  PRINT
  Mat 0
  CsTally 0

