.. _section_eng_mesh:

Mesh
==============

In Monte Carlo calculations, especially for calculations that introduce thermal feedback, mesh-based parameters
are often utilized as inputs for information such as temperature/density, etc. Based off user-specified information,
the Mesh input option can read pre-defined mesh parameters.


.. _section_eng_mesh_file:

Mesh File Format
----------------

RMC can read mesh files in HDF5 format. The generation method of HDF5 files can be obtained from the Internet, hence it
will not be further elaborated in this manual. The HDF5 mesh file read by RMC needs to contain at least two sets of
content, namely the Geometry Group and Mesh Dataset:

- The name of the geometry group is limited to "Geometry". The group needs to contain an attribute, where the attribute
  is named "MeshType" and the value of the attribute is 1. This indicates that the mesh is a three-dimensional uniform
  mesh. RMC intends to add the capability of reading other types of mesh files in the future. The geometry group also
  requires two different sets of data: Firstly, an array titled "BinNumber" with the size of 3. This array indicates the
  number of meshes in the X/Y/Z directions respectively. Secondly, a 3-by-2 matrix titled "Boundary". This matrix indicates the
  maximum and minimum boundaries of each dimension of X/Y/Z respectively.

- The mesh Dataset has no restrictions on name and quantity. However, the amount of data within the Dataset must be
  consistent with the data requirements specified within the Geometry Group.

.. _section_eng_mesh_block:

Mesh Input Card
-----------------

.. code-block:: none

  MESH
  MeshInfo <id> [type=<type>] [filename=<filename>]
  [datasetname=<datasetname>]

where，

-  **Mesh** \ is the keyword for the Mesh module;

-  **MeshInfo** \ is the keyword for the Mesh information input option;

-  **id** \ is the Mesh ID. The ID can be used as an input for Mesh Temperature, Mesh Density, etc;

-  **type** \ refers to the Mesh type. As RMC currently supports only the three-dimensional uniform mesh type, the type
   value is fixed at \ **type=1** \;

-  **filename** \ refers to the name of the Mesh file;

-  **datasetname** \ refers to the name of the Dataset. The user can define more that one Datasets in the same mesh file,
   but each MeshInfo input option can only utilize a single Dataset.

Mesh Module Input Example
-------------------------

.. code-block:: c

  Mesh
  MeshInfo 1 type=1 filename=Example1.h5 datasetname=Temperature
  MeshInfo 2 type=1 filename=Example2.h5 datasetname=Temperature
  MeshInfo 3 type=1 filename=Example2.h5 datasetname=Density

The Mesh module above contains a total of three meshes: the first mesh is read from Example1.h5 file, where the data is
from the Temperature dataset; the second and third meshes are both read from Example2.h5 file, where the data are from
the Temperature and Density data sets respectively.

