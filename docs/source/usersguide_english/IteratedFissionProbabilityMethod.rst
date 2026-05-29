.. _section_eng_ifp:

Calculations for Iterated Fission Probability Method
====================================================

The RMC Iterated Fission Method can calculate dynamic parameters and geometric perturbation coefficients in the
criticality and burnup calculation modes. The geometric perturbation coefficients are only used for
continuous energy cross sections, while the dynamic parameters can also be applied to multi-group cross sections.

Iterated Fission Probability Method module Input Card
-------------------------------------------------------

.. code-block:: none

    Adjoint
    blocksize=<param>
    fisbranch=<param>
    OUTPUTINTERVAL=<param>
    kinetic
    AdjointFluxMesh [Energy=<enenrgy>]  [Scope=<scope>] [Bound=<bound>]
    GeoAdjointTally <param> surf=<param> type=<param> parameters=<param> constraint=<param> universe=<param> cellmat=<param> cutoffcos=<param>

where,

-  **Adjoint** \ is the keyword for the Iterated Fission Probability Method module;

-  **blocksize** \ specifies the number of active generations that constitute a block, with the default being 10;

-  **fisbranch** \ specifies how many fission neutrons each fission may produce, with the default value being 1.
   The larger the value, the larger the initial memory allocation of the Iterated Fission Probability Method;

-  **OUTPUTINTERVAL** \ specifies output interval for the adjoint calculation results, where the output interval
   is in terms of the number of generations. By default, the output is produced after all active generations;

-  **kinetic** \ is the keyword for the calculation of dynamic parameter(s);

-  **AdjointFluxMesh** \ is the keyword for input sub-options for solving critical adjoint fluxes
   for the iterated fission probability method. This input option cannot be used with the \ **kinetic** \,
   \ **GeoAdjointTally** \, and sensitivity and uncertainty analysis functions.

   -  **Energy** \ specifies energy cutoff points for the adjoint flux;

   -  **Scope** \ specifies the number of mesh elements in the x, y, and z directions. In particular, a parameter
      of "-1" means there is only one infinite mesh in that direction (Note: in the Scope input option of the Universe
      Iterated Geometry, a parameter of 1 means there is only one infinite mesh in that direction);

   -  **Bound** \ specifies the boundary range of the mesh in the x, y, and z directions, in the form of
      "Bound = x_min x_max y_min y_max z_min z_max". If there is only one layer of mesh in a certain direction,
      the corresponding parameters in the \ **Bound** \ option have no practical meaning.

-  **GeoAdjointTally** \ is the keyword for the geometry perturbation card.

   -  **surf** \ indicates the surfaces of the calculated geometric perturbation coefficients, which are
      correspondingly indicated in the Surf input card;

   -  **type** \ refers to the Geometric transformation type. 1: translation, 2: uniform expansion,
      3: unidirectional expansion, 4: rotation;

   -  **parameters** \ refers to parameters describing geometric transformation. Translation requires three
      parameters, i.e., the normalized vector pointing in the translation direction;
      uniform expansion does not require parameters; unidirectional expansion requires three parameters,
      i.e., the normalized vector pointing in the expansion direction; rotation requires 7 parameters, i.e.,
      the normalized direction vector of the rotation axis, the coordinates of the fixed point of rotation,
      and the rotation direction (positive 1 if the right-hand rule is met, negative 1 if the rule is not met);

   -  **constraint** \ indicates that the cell is a constrained cell. Perturbations occur only towards the
      surfaces within the constrained cell. The input format is the same as the cell input card;

   -  **universe** \ specifies the universe in which the constrained cell is located;

   -  **cellmat** \ is used to limit the perturbed portion of the surface. Positive numbers represent mesh
      elements, and negative numbers represent materials. These are all internal program identifiers;

   -  **cutoffcos** \ indicates the cutoff value for the value of the cosine passing through the surface.

Dynamic parameter output file
-----------------------------

The output gives the mean and standard deviation of the following dynamic parameter results: effective neutron
generation time, where the units are in microseconds; rossi-alpha, where the units are 1/microsecond;
effective delayed neutron fraction, where the units are 1.

.. code-block:: none

    ------------------ Current Cycle Number = 50 ------------------

    ------------------ kinetic parameters, gen. time, rossi-alpha, beta-eff  ------------------
                              Ave          std. dev.
    gen. time    (us)    2.39916E+01     1.23582E+00
    rossi-alpha (us-1)  -3.32090E-04     1.19101E-04
    beta-eff             8.47770E-03     3.27803E-03

Geometry perturbation coefficients output file
----------------------------------------------

The output gives the mean and relative error of the geometric perturbation coefficient results:
transport term; scattering and fission terms. The total result is the sum of the two. In the example shown below,
the geometric perturbation coefficient is 0.013019.

.. code-block:: none

    ------------------ Current Cycle Number = 300 ------------------

    ------------------ geometrical perturbation parameters ------------------
                              Ave          Re
    transport:          2.74153E-04     5.18707E-01
    scatter + fission:  1.27445E-02     8.43237E-01

