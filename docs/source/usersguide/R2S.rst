.. _section_eng_r2s:

R2S Shutdown Dose Rate Calculation
===========================================

.. _section_eng_r2s_intro:

Workflow Overview
--------------------------------

R2S (Rigorous Two-Step) is used to calculate shutdown photon dose rates. The RMC Python control module completes the
two-step workflow automatically:

1. **Step 1, neutron transport and activation** \ : a neutron fixed source drives burnup/activation, producing activation
   products in the activation cells and writing the shutdown decay photon source to ``<inp>.Result.h5``.
2. **Step 2, photon transport** \ : the decay photon source from Step 1 is read automatically as a photon fixed source.
   The same geometry and materials are reused, and the specified tally outputs the dose rate directly.

The relationship is: neutron transport gives neutron flux, activation gives activation products and decay, the decay
photon source becomes the second-step photon source, and photon transport converts photon flux to dose rate.

The user only needs to prepare one Step-1 activation input file and add an :ref:`R2S control card <section_eng_r2s_card>`.
The Step-2 photon transport input is generated and run automatically.

The Python workflow supports both the conventional scalar source at ``Burnup/StepN/GammaSource`` and direct reading
of the full-order source at ``Burnup/StepN/FETGammaSource``. The FET source retains spatial modes inside each coarse
region. See :ref:`section_eng_r2s_fet_source` for manual and automatic workflows.

.. _section_eng_r2s_step1_input:

Step-1 Input Requirements
----------------------------------------

The first R2S step must be a fixed-source burnup/activation calculation, not a criticality burnup calculation. The input
file should contain at least:

-  \ **FixedSource** \ : defines the number of particles for Step-1 neutron transport;
-  \ **ExternalSource** \ : defines the neutron fixed source;
-  \ **BURNUP** \ : uses \ **ActivationCell** \  to specify activation cells, \ **TimeStep** \  to specify activation/decay
   step lengths, \ **SourceRate** \  to specify neutron source rates, and \ **DecaySource** \  to specify the cells whose
   decay photon source should be output;
-  \ **Tally** \ : defines the photon cell or mesh tally used for the final dose rate, and the \ **R2S** \  card refers to it
   through \ **DoseTally** \ .

The updated ``neutron_hdf5`` spectra include lambda in their line intensities. Use the default ``LibType = 0``; the option may be omitted.
The ``Spectrum = fine`` or ``Spectrum = bin`` option in the \ **R2S** \  card controls how the second-step photon fixed
source is generated: ``fine`` uses the point-wise decay photon lines from ``.Result.h5``, while ``bin`` collapses them into
the energy bins specified by the \ **Energy** \  option in the \ **R2S** \  card. In other words, \ **DecaySource** \  writes
the decay photon source to the result file, and \ **R2S** \  chooses its fine or binned representation in the second-step
input. For automated R2S, \ **DecaySource** \  must omit ``CustomBin`` or set ``CustomBin = 0`` so
that ``.Result.h5`` retains the fine lines. If the user only needs a binned decay source directly in the burnup result file,
set ``CustomBin = 1`` and ``Energy`` in \ **DecaySource** \ .

A key Step-1 input fragment is shown below:

.. code-block:: none

    FixedSource
    Particle population = 100000

    ExternalSource
    Source 1 particle = 1 position = 5 15 5 energy = 14

    BURNUP
    ActivationCell  1 2 3 4 5
    TimeStep        3.5 1.5 5 5
    SourceRate      1e10 1e10 2.5e10 2.5e10
    CoupleMode      2
    DecaySource type = 1 cell = 1 2 3 4 5

    Tally
    Meshtally 2 type = 1 particle = 2 Geometry = 1 Bound = 0 20 0 20 0 10 Scope = 2 2 1
        energy = 0.01 0.02 0.05 0.1 0.2 0.5 1 2 4 8 10

    R2S
    Step       = 4
    Population = 1000000
    Spectrum   = fine
    DoseTally  = mesh 2

.. _section_eng_r2s_card:

R2S Control Card
-------------------------------

Add an \ **R2S** \  module to the Step-1 input card to trigger the automated workflow. This card is read only by the
Python control module and is not written to the input card passed to the RMC executable.

.. code-block:: none

    R2S
    Step       = 4
    Population = 1e7
    Spectrum   = fine
    DoseTally  = mesh 2

Options:

-  **Step** \ : selects which burnup/activation step provides the decay photon source for Step 2. It must be a
   nonnegative integer no greater than the number of irradiation and decay steps, and the selected step must contain
   a usable decay photon source. The default is the final step, including a final cooling step.

-  **Population** \ : number of particles for Step-2 photon transport, an integer of at least 101.
   The default is the ``population`` in Step-1 \ **FixedSource** \ .

-  **Spectrum** \ : spectrum representation of the Step-2 photon source.

   - ``fine`` (default): uses discrete photon lines. Scalar sources generate Distribution ``type = 1``; full-order
     FET sources are read directly through ``FETGAMMASOURCE``;
   - ``bin``: applies only to scalar sources and groups them into the boundaries given by \ **Energy** \ , using
     Distribution ``type = 4``. \ **Energy** \ is required. FET automation supports only ``fine``.

-  **Energy** \ : gives at least two finite, nonnegative, strictly increasing energy boundaries in MeV when
   ``Spectrum = bin``. Omit this option for FET automation.

-  **DoseTally** \ : specifies the tally used for final dose-rate calculation. The format is
   ``DoseTally = <type> <id>``, where ``type`` is ``cell`` or ``mesh`` and ``id`` is the tally id. See
   :ref:`section_eng_r2s_dosetally`.

.. _section_eng_r2s_dosetally:

Dose-Rate Tally
-------------------------------

The dose rate is scored directly by the tally in Step-2 photon transport. The user should:

1. Define a **photon** tally (``particle = 2``) in the \ **Tally** \  module of the Step-1 input card, including the geometry
   range and energy groups. Either a cell tally or a mesh tally can be used;
2. Use \ **DoseTally** \  in the \ **R2S** \  card to specify the tally type and id.

Select the ICRP-119 flux-to-dose coefficients with ``DOSERATE`` on the designated CellTally or MeshTally:

- ``DOSERATE = ICRP-119-AP``: antero-posterior irradiation (AP);
- ``DOSERATE = ICRP-119-ISO``: isotropic irradiation (ISO).

R2S preserves this choice when generating the Step-2 input. If the selected tally has no ``DOSERATE`` option,
R2S adds ``ICRP-119-AP`` by default. For a separately run photon stage, specify the option directly on the tally.
Different tallies in the same input may select AP or ISO independently.

For example, append ``doserate = ICRP-119-ISO`` to MeshTally 2 below to select ISO while keeping
``DoseTally = mesh 2`` unchanged. The same ``doserate`` syntax applies to CellTally.

Dose-rate tallies require photon flux (``type = 1 particle = 2``) and finite,
positive, strictly increasing ``Energy`` upper bounds. ``Time``, ``Integral``, ``Bin`` references and ``Nest``
are unsupported, as are combinations with ``Dose``, ``Gaussian``, ``Multiplier``, ``Attenuator``, ``Absolute``
or ``MCNPFormatOutput``. Cell volumes use tally ``Volume`` when supplied, otherwise geometry ``Vol``;
values must be finite and positive. Using geometry ``Vol = 1 cm^3`` emits a warning and continues, following
the burnup convention; omitted ``Vol`` defaults to this value. Meshes require finite bounds and positive volumes.
MeshTally ``DOSERATE`` only supports the track-length estimator (``Estimator = 1``, the default).

For example, a 2 x 2 mesh dose-rate tally can be specified as:

.. code-block:: none

    R2S
    Step      = 4
    Spectrum  = fine
    DoseTally = mesh 2

    Tally
    Meshtally 2 type = 1 particle = 2 Geometry = 1 Bound = 0 20 0 20 0 10 Scope = 2 2 1
        energy = 0.01 0.015 0.02 0.03 0.04 0.05 0.06 0.08 0.1 0.2 0.4 0.6 0.8 1 2 4 6 8 10

The tally result is written to the Step-2 ``.Tally`` file. For each cell or mesh element, one line is printed after the
energy groups and the ``Tot`` line:

.. code-block:: none

    Dose Rate (ICRP-119-AP) =   6.3655E-03 +/- 4.9759E-05 Sv/hr   (RE = 0.0078)

The output label identifies ``ICRP-119-AP`` or ``ICRP-119-ISO``; both the dose mean and total uncertainty use the selected coefficients.

The dose rate is calculated as
``dose rate = (flux / volume) x source strength x conversion factor(E) x 3.6e-9``, in Sv/hr. The source strength is the
total strength of the Step-1 decay photon source, and the volume is the corresponding cell or mesh-element volume.
Conversion accounts for the volume normalization actually applied during scoring, so volume is divided out only once.
Total-dose uncertainty is calculated from the energy-weighted sum for each photon history, including correlations
between energy groups; the source strength and source spectrum are treated as given.

.. _section_eng_r2s_run:

Execution and Output
-----------------------------------

Run R2S through the RMC Python module, the same way as ordinary burnup calculations. When the input card contains an
\ **R2S** \  module, the program automatically runs the two steps in sequence:

- Step-1 activation results are archived under ``archive/burnup/``. The decay photon source is located in
  ``Burnup/StepN/GammaSource`` in ``<inp>.burnup.Result.h5``; full-order FET sources use ``Burnup/StepN/FETGammaSource``;
- Step-2 photon transport results are archived under ``archive/r2s_photonN/``, where ``N`` is the selected activation step.
  The dose rate is written to the ``.Tally`` file in that directory;
- The run log prints the total decay photon source strength for the selected step, for example
  ``R2S: total decay photon source strength at Step4 = 2.614183E+08``.


.. _section_eng_r2s_fet_source:

Full-Order FET Decay Photon Source
------------------------------------------

\ **DECAYSOURCE** \ is off by default for FET burnup. The full-order FET decay photon source is written to
``Burnup/StepN/FETGammaSource`` in ``<inp>.Result.h5`` only when this card is explicitly present in the Step-1
\ **BURNUP** \ module. The supported scope is fixed-source ``SOURCERATE`` activation, ``COUPLEMODE 2`` (SIMPLE),
and discrete photon lines. For direct transport, omit ``ENERGY`` and either omit ``CUSTOMBIN`` or set it to ``0`` in
\ **DECAYSOURCE** \ . The direct sampler accepts local bounded source regions with second-order radial and first-order
axial expansions (R2-A1). All source-bearing regions must share the same axis and origin, must not overlap in R-A
source space, and must use local coordinates equal to global coordinates. Untransformed nested universes are accepted;
translated, rotated, or lattice instances are not supported for direct transport.
Other supported orders may still be written, but they cannot be transported directly through this interface.

Step 2 samples ``r^2`` and axial position from the full-order FET source density, with uniform azimuth and an isotropic
outgoing direction.
To keep the sampled source density nonnegative, the reader uniformly scales each energy line's higher-order
coefficients as needed, preserving each line's volume-integrated intensity and the total source strength.

In ``Burnup/StepN/FETGammaSource/<field>``, ``EnergyMeV`` contains the line energies.
``CoefficientPhotonsPerSecond`` stores coefficients in photon/s, with energy as the outer index and mode as the
inner index. ``ModeOrders`` defines the ordering: (0,0), (0,1), (1,0), (1,1), (2,0), (2,1) for R2-A1.
Each line's zero-order coefficient is the total photon emission rate at that energy in the region, equal to the
corresponding ``VolumeIntegratedPhotonsPerSecond`` entry. Reconstruct all modes and divide by ``VolumeCm3`` to obtain
the local source density in photon/(s cm³).
``TotalVolumeIntegratedPhotonsPerSecond`` is the field total, and the collection attribute ``TotalPhotonsPerSecond``
is the sum over fields. A field without emission lines retains ``EnergyCount=0`` and a zero total; energy and
spectrum arrays are omitted. ``DirectTransportReady=1`` indicates compatibility with the direct transport interface.

The following continues the example in :ref:`section_eng_burn_fet`, where cell 4 is irradiated for 0.1 d and cooled
for 10 d. Select ``Step2``, the end of cooling. Assume the Step-1 input is named ``activation`` and both inputs
are in the same directory. Retain the geometry and materials, remove the \ **BURNUP** \ module, and use the following
photon transport, source, and tally blocks for Step 2:

.. code-block:: none

    FixedSource
    Particle population = 1000000

    ExternalSource
    FETGAMMASOURCE FILE=activation.Result.h5 STEP=2

    Physics
    ParticleMode P
    Photon PPRODUCEE=1 TTB=1

    Tally
    CellTally 2 Type=1 Particle=2 Cell=4 Volume=26.4050862534
        Energy=0.01 0.1 0.5 1 2 5 10 20 DoseRate=ICRP-119-ISO

Cell 4 has a radius of 0.41 cm, a height of 50 cm, and a volume of 26.4050862534 cm\ :sup:`3`.
``CellTally 2`` scores photon flux in this cell and outputs the dose rate using ISO coefficients.
Set ``DoseRate=ICRP-119-AP`` to use AP coefficients.

``FILE`` points to the Step-1 ``Result.h5`` and must not contain whitespace. A relative path is resolved from the
directory containing the Step-2 input. Write ``FILE=...`` and ``STEP=...`` without internal spaces.
The suggested card printed by Step 1 uses only the result basename; use the correct relative or absolute path
when the two inputs are in different directories. ``STEP`` is a nonnegative integer selecting ``N`` in
``Burnup/StepN/FETGammaSource``. Each \ **EXTERNALSOURCE** \ block accepts only one \ **FETGAMMASOURCE** \ card.
This source is supported only in photon-only fixed-source mode and cannot be combined with ``SOURCE``,
``SURFSRCREAD``, ``DISTRIBUTION``, ``H5SOURCEINFO``, or ``TRANSFORM`` cards.

For compatibility with the previous workflow, an existing
``RMC_FET_GAMMA_EXTERNAL_SOURCE_V1`` source-ready sidecar may still omit ``STEP``:

.. code-block:: none

    ExternalSource
    FETGAMMASOURCE FILE=fet_gamma_source_ready.h5

This FILE-only form is for legacy sidecars. New calculations should prefer ``Result.h5 + STEP`` to avoid an additional
source-conversion step.

The automatic workflow uses the same Python \ **R2S** \ controller. For this example, retain the neutron source and
\ **BURNUP** \ module in the Step-1 input, add the following photon tally to its \ **Tally** \ module, and append the
\ **R2S** \ control card:

.. code-block:: none

    Tally
    CellTally 2 Type=1 Particle=2 Cell=4 Volume=26.4050862534
        Energy=0.01 0.1 0.5 1 2 5 10 20 DoseRate=ICRP-119-ISO

    R2S
    Step = 2
    Population = 1000000
    Spectrum = fine
    DoseTally = cell 2

FET automation accepts only ``Spectrum = fine``. Omit source ``Energy`` boundaries from both \ **R2S** \ and
\ **DECAYSOURCE** \ ; the dose tally still requires its own ``Energy`` bins. The selected cell/mesh tally retains
``DOSERATE=ICRP-119-AP`` or ``DOSERATE=ICRP-119-ISO``, defaulting to AP when omitted.

The controller reads ``FETGammaSource`` at the selected step, checks ``DirectTransportReady`` and the total source
strength, and generates ``FETGAMMASOURCE FILE=... STEP=...``. RMC reads the spatial coefficients and absolute source
strength directly, without scalar conversion or another decay-constant factor. Missing full-order sources or sources
that are not ready for direct transport cause an error.

The generated ``FILE`` is an absolute path to the result in ``archive/burnup/``. The Step-2 input remains runnable
after it is archived under ``archive/r2s_photonN/``. Retain the referenced HDF5 file for independent runs. When moving
the archive or transferring it to another machine, copy the source file as well and update ``FILE``. The photon stage
continues to use the material composition from the Step-1 input.

.. _section_eng_r2s_database:

Decay Photon Spectrum Database
---------------------------------------------

Burnup/activation and point burnup both read decay photon spectra from ``GammaSpectrum/spectrum`` in ``neutron_hdf5``.
Energies are in MeV. Updated line intensities are in photons/(atom s), include the decay constant lambda, and require no
additional multiplication by lambda.

The \ **LibType** \  option in the Step-1 \ **BURNUP** \  card \ **DecaySource** \  specifies the intensity convention;
see :ref:`section_eng_burn_cards`.

-  **LibType = 0** \  (default): line intensities include lambda, as in the updated ``neutron_hdf5`` library;
-  **LibType = 1** \ : compatibility with older libraries whose intensities are in photons/decay and exclude lambda.
   RMC supplies lambda in this case.

Point-burnup \ **GAMMASPECTRUM** \  requires intensities that include lambda. Set \ **GammaLibPath** \  to the same
``neutron_hdf5`` directory used for burnup/activation.

.. important:: ``LibType`` selects intensity units, not a directory name or file format. Using ``LibType = 1`` with
   intensities that include lambda counts lambda twice.

Step 2 requires photon and accompanying electron data for the material elements. The library versions are selected
from the matching entries in ``xsdir``; photon suffixes may be ``.04p`` or ``.80p``. Step 1 uses neutron data
such as ``.32c``, ``.71c``, or ``.830c``. The two steps can share one ``xsdir``: photon transport matches the
corresponding photon/electron element libraries automatically, without rewriting the material cards for Step 2.
