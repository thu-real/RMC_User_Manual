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

-  **Step** \ : selects which burnup/activation step provides the decay photon source for Step 2. The default is the last
   activation step.

-  **Population** \ : number of particles for Step-2 photon transport. The default is the ``population`` in Step-1
   \ **FixedSource** \ .

-  **Spectrum** \ : spectrum representation of the Step-2 photon source.

   - ``fine`` (default): uses point-wise fine photon lines, corresponding to Distribution ``type = 1``;
   - ``bin``: collapses the photon source into the energy boundaries given by \ **Energy** \ , corresponding to Distribution
     ``type = 4``. The \ **Energy** \  option must be provided in this mode.

-  **Energy** \ : gives increasing energy boundaries in MeV when ``Spectrum = bin``.

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
  ``Burnup/StepN/GammaSource`` in ``<inp>.burnup.Result.h5``;
- Step-2 photon transport results are archived under ``archive/r2s_photonN/``, where ``N`` is the selected activation step.
  The dose rate is written to the ``.Tally`` file in that directory;
- The run log prints the total decay photon source strength for the selected step, for example
  ``R2S: total decay photon source strength at Step4 = 2.614183E+08``.

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

Photon transport in Step 2 requires photon (``.04p``) and electron (``.03e``) data for the material nuclides. Neutron
activation in Step 1 uses neutron data such as ``.32c`` or ``.60c``. The two steps can share one ``xsdir`` file: photon
transport maps neutron nuclide ids to the corresponding photon/electron element libraries automatically, for example
``26056.32c`` to ``26000.04p``. The user does not need to rewrite materials separately for photon transport.
