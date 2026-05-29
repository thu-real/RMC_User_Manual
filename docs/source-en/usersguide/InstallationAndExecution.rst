.. _section_eng_run_exe:

Running the RMC Code
=====================

After compiling or obtaining the RMC executable code suitable for the current system from the package, 
and completing the configuration of the database and writing the input file, you can run the RMC code for calculation.

This section introduces how to run the code, and the subsequent chapters introduce the method of writing input files.


Installation and Configuration
-------------------------------

Before running the code, you need to configure the cross-section database.

When RMC adopts the ACE format subreaction database, it obtains the database location through the index file and then reads the data.

 - Copy the database (Take Library folder as an example) to a directory on the computer.

 - Open the corresponding index file xsdir with a text editor, and set the **full file directory path** where the database file is located, 
   such as "DATAPATH = E:\Library\endf7_2" (Windows) or "DATAPATH = /home/username/Library/endf7_2" (Linux).

 - Place the input file in the directory of the RMC executable code to run the example. Note that the user needs to let the code know the specific path 
   of the database index file "xsdir" so that RMC can call the database. Currently, RMC supports the following ways to specify the path of "xsdir":

   (1)By Command line:

   .. code-block:: none

     mpiexec –n **number_of_MPI_processes** RMC_MPI_OMP -s **number_of_OMP_threads** **input_filename** -d **xsdir_file_path**

   (2)Adding environment variable "RMC_DATA_PATH", such as:

   .. code-block:: none

     RMC_DATA_PATH=E:\\RMC_DATA (windows)
     RMC_DATA_PATH=/home/username/RMC_DATA (Linux)

   (3)Using the default path, which is the current working directory:

     The user should ensure that the "xsdir" index file is located in the current working directory.

When RMC adopts the HDF5 format AIS neutron reaction database, it directly reads the data according to the database path.
Currently, RMC supports the following two ways to specify the HDF5 format AIS database path:

   (1)By Command line:

   .. code-block:: none

     mpiexec –n **number_of_MPI_processes** RMC_MPI_OMP -s **number_of_OMP_threads** **input_filename** -d **database_path**

   (2)Adding environment variable "RMC_DATA_PATH", such as:

   .. code-block:: none

     RMC_DATA_PATH=E:\\RMC_DATA (windows)
     RMC_DATA_PATH=/home/username/RMC_DATA (Linux)

   Note: The above database path should be the directory containing the HDF5 format AIS database. The program will automatically
   search for the directory named "AISNucDatabase" under this directory, and index the specific data files in the "AISNucDatabase"
   directory according to the parameters (isotope name, database version) specified in the material module of the RMC input card.


Hash Data Type Definition
~~~~~~~~~~~~~~~~~~~~~~~~~~~~
When the cell hierarchy defined by the user's geometry is very high, the hash data type using unsigned long long (64-bit) may not be sufficient. 
In this case, you need to input the command at the compilation stage:

.. code-block:: none
   
  cmake -Dhash128=on ..

to enable the 128-bit hash data option.


Running Commands
-----------------

Serial Execution
~~~~~~~~~~~~~~~~~~

Assuming the serial version of the RMC executable code is named "RMC", the method to run RMC serially is enter the directory where the RMC executable code 
is located through the Windows command console or Linux terminal, and enter the following command:

.. code-block:: none

  Windows system: RMC [OPTION...] input_filename

  Linux system: ./RMC [OPTION...] input_filename


**Note**:

    - The input file should be in the same directory as the RMC executable code, otherwise the full file path should be used.

    - The output file name can be omitted, and the code will default to the input file name with the suffix ".out" as the output file name. 
      For burnup calculations, the code will also automatically specify suffixes such as ".nuc" and ".power" as additional output files, and 
      users should avoid conflicts in output file names.

    - *If the input file name has a suffix, it should include the suffix*\ .

    - *It is not recommended to mix input files from Windows and Linux systems*\. Windows system defaults to ANSI file encoding (GBK encoding in Chinese 
      operating systems), while Linux system defaults to UTF-8 file encoding. Mixing them may cause file reading failures. If necessary, it is recommended to 
      use software like `UltraEdit`_ or `VSCode`_ to manually convert the encoding format.


MPI Parallel Execution
~~~~~~~~~~~~~~~~~~~~~~~

To run RMC in parallel, you need to configure the MPI parallel environment in the operating system. Software such as mpich, impi, openmpi, 
and mvapich can provide an MPI parallel environment.

Take MPICH2-1.4.1p1 on Windows as an example. Assuming the parallel version of the RMC executable code is "RMC_mpi", the command to execute 
parallel calculation is:

.. code-block:: none

  mpiexec -n number_of_parallel_cores RMC_mpi [OPTION...] input_filename

For example, **mpiexec –n 10 RMC_mpi -o output input** indicates using 10 processes to calculate, the input file name is "input", and the output file name is "output".


MPI/OpenMP Hybrid Parallel Execution
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

Assuming the hybrid parallel version of the RMC executable code compiled with both MPI and OpenMP is "RMC_MPI_OMP", the command to execute parallel calculation is:

.. code-block:: none

  mpiexec –n number_of_MPI_processes RMC_MPI_OMP -s number_of_OMP_threads input_filename

For example, **mpiexec –n 2 RMC_MPI_OMP -s 10 input -o output** indicates using 2 processes, each with 10 threads to calculate, the input file name is "input", 
and the output file name is "output".


Python Invocation of RMC Execution
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

The RMC code supports users to invoke the Python module for calculation. The calculation process is as follows:

  - **Install the Python module**: Enter the root directory of the RMC code and execute "python setup.py install" to install the Python dependencies of RMC 
    (or copy the **RMC** folder under the root directory of the RMC project to the execution directory).

  - **Configure the working directory file**: Assuming the user execution directory is named "directory", enter this directory, create a "runner.py" file 
    (or copy "runner.py" from the **RMC** folder under the root directory of the RMC code to the directory, recommended!!!), create a "workspace" folder, 
    copy the RMC executable code, the database files required for the code to run, such as "xsdir, xsdir_sab, DepthMainLib, RMC_DATA.h5", etc. to the "workspace" 
    folder, and place the RMC code input card inp in the "workspace" folder.

  - **Set calculation conditions**: Users can set calculation conditions by setting relevant parameters in the "runner.py" folder. An example of the "runner.py" file is as follows:

    .. code-block:: none

      run(inp="workspace/inp", n_mpi=4, n_threads=None, exec="workspace/RMC", 
          status="workspace/status.txt", conti=False, archive_dir=archive, platform='linux')
      
    Among them, **inp** specifies the input file (default is workspace/inp), **n_mpi** specifies the number of parallel cores, **n_threads** specifies the number 
    of openmp processes, **exec** specifies the location of the RMC executable code (default is workspace/RMC), **status** specifies the output status file, 
    **conti** indicates whether to perform continuation calculation, **archive_dir** specifies the storage path of the output file, and **platform** specifies 
    the calculation platform, including "linux", "tianhe", "yinhe", etc.
  
  - **Calculation**: Users can execute **python runner.py** in the directory to execute.


Test Input and Output
------------------------

The Example folder in the code release package contains input and output files of typical examples. Users can calculate some examples 
(such as "3_1_PWR_assembly, 8_1_Burn_PWR_Pin") to verify whether the code is correctly installed.


Trial Version Limitations
----------------------------

Trial version users should note that the maximum number of parallel MPI processes for the trial version of RMC is 8, and the maximum runtime is 
1000 core hours. Exceeding the maximum runtime will cause the code to interrupt and exit without outputting the calculation results. This duration 
is sufficient to complete the full life cycle burnup calculation of the assembly or multi-step burnup calculation of the core.

The trial version does not support the following calculation functions: sensitivity and uncertainty analysis, stochastic media and dispersion media, 
criticality search, spatiotemporal kinetics calculation, group constants, region decomposition, tally data decomposition, stochastic neutron kinetics, 
cross-section parameterization, particle event tracking (marked in the directory title).

If you have additional requirements for calculation time or functions, please contact the REAL team (contact@reallab.org.cn, https://forum.reallab.org.cn) 
to negotiate an enterprise version or temporary trial version.

.. _UltraEdit: https://www.ultraedit.com
.. _VSCode: https://code.visualstudio.com
