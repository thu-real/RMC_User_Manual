.. _section_eng_conti_calc:

Checkpoint Continuation Calculation 
=====================================

RMC supports the output function for continuation files. The continuation function is used to restart calculations after RMC stops computing.

Continuation Calculation Module Input Options
---------------------------------------------

.. code-block:: none

    BINARYOUT
    RESTARTBINARY Write =<flag> Cycles=<cycles_vector_group> Interval=<N> Overwrite=<flag>

Among them,

-  **RESTARTBINARY** \  is the keyword for the continuation calculation input option;

-  **Write** \  specifies whether to output continuation files, 1 indicates output, 0 indicates no output, the default value is 0;
   When this option is 1, either \ **Cycles** \  or \ **Interval** \  input option must be defined;

-  **Cycles** \   is only for criticality calculation mode, specifying in which cycles to output data;

-  **Interval** \   applies to both criticality and fixed source calculations: in criticality calculations, it specifies after how many generations 
   to output continuation calculation files; in fixed source calculations, it specifies after how many particles to output continuation calculation files;

-  **Overwrite** \   is an option set in criticality calculations to reduce storage space usage. It can only be used when using the\ **Interval** \ option.
   1 indicates each output file overwrites the previous output file; 0 indicates no overwriting, the default value is 0.

Continuation Calculation File Usage
------------------------------------

The specific running process of continuation calculation is basically the same as running RMC, except that "-r restart_file_name" is added at the end.
Assuming the serial version of RMC executable is named "RMC", the method for serial running is: enter the following command in the directory where the RMC executable is located:

.. code-block:: none

   Windows environment: RMC(space)-r(space)restart_file_name
   Linux environment: ./RMC(space)-r(space)restart_file_name

The following is an example of the continuation calculation function. This example produces an output in the form of a continuation file every 1000 particles during the fixed source mode.
BINARYOUT
RESTARTBINARY Write = 1 Interval = 1000